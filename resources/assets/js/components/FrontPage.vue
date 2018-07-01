<template>
	<div>
		<navbar @search="onSearch($event)" :isLoadingSearch="isLoadingSearch"></navbar>
		<main-map :center="center" :markers="markers" :complaints="complaints" @reload="onReload()"></main-map>
	</div>
</template>

<script>
import L from 'leaflet';

export default {

	data () {
		return {
			center: L.latLng(-19.9192, -43.9386),
			complaints: [],
			isLoadingSearch: false,
			markers: []
		}
	},

	mounted() {
		this.loadComplaints();
	},

	methods: {
		loadComplaints() {
			this.complaints = [];
			axios.get('/tretas')
				.then(response => {
					for (let complaint of response.data) {
						complaint.position = L.latLng(complaint.latitude, complaint.longitude);
						this.complaints.push(complaint);
					}
				}).catch(error => {
					console.error(error);
				});
		},

		onReload() {
			this.markers = [];
			this.loadComplaints();
		},

		onSearch(event) {
			this.isLoadingSearch = true;
			this.markers = [];
			axios.get('https://nominatim.openstreetmap.org/search/', { params: { format: 'json', street: event, city: 'Belo Horizonte', state: 'Minas Gerais', country: 'Brasil'} })
				.then(response => {
					let points = response.data;
					if (points.length > 0) {
						for (let point of points) {
							let marker = {
								position: L.latLng(point.lat, point.lon),
							}
							this.markers.push(marker);
						}
						this.center = _.cloneDeep(this.markers[0].position);
					}
					this.isLoadingSearch = false;
				}).catch(error => {
					this.isLoadingSearch = false;
					console.error(error);
				});
		}
	}
}
</script>
