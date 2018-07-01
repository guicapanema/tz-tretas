<template>
	<div class="main-map">
		<l-map ref="map" style="height: 100%; width: 100%" :zoom="zoom" :center="center" @click="onAddMarker($event)">
			<l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
			<marker-popup
				v-for="marker of markers"
				:ref="'marker' + marker.id"
				:key="marker.id"
				:position="marker.position"
				@save="onSaveMarker($event)">
			</marker-popup>
			<marker-popup
				v-for="complaint of complaints"
				:complaint="complaint"
				:icon="getIcon(complaint)"
				:key="complaint.id"
				:position="complaint.position">
			</marker-popup>
			<l-icon-default :image-path="path"></l-icon-default>
		</l-map>
	</div>
</template>

<script>
import { LMap, LTileLayer, LIconDefault } from 'vue2-leaflet';
import "leaflet/dist/leaflet.css";

export default {
	components: {
		LMap,
		LTileLayer,
		LIconDefault,
	},

	props: ['center', 'complaints', 'markers'],

	data () {
		return {
			attribution: '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
			iconCobrador: null,
			iconComplaint: null,
			iconCut: null,
			iconDelay: null,
			path: '/images/',
			url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
			zoom: 13,
		}
	},

	mounted() {
		this.setupIcons();
	},

	methods: {
		getIcon(complaint) {
			switch (complaint.type) {
				case 'geral':
					return this.iconComplaint;
					break;
				case 'atraso':
					return this.iconDelay;
					break;
				case 'corte':
					return this.iconCut;
					break;
				case 'cobrador':
					return this.iconCobrador;
					break;
				default:
					return null;
					break;
			}
		},

		onAddMarker(event) {
			console.debug(event.latlng);
			this.markers = [];
			let markerId = this.complaints.length + 5;
			let marker = {id: markerId, position: event.latlng };
			this.markers.push(marker);

			// setTimeout(() => {
			// 	let popup = this.$refs['marker' + markerId][0].$refs.popup;
    		// 	popup.mapObject.openOn(this.$refs.map.mapObject);
			// }, 1200);

		},

		onSaveMarker(complaint) {
			this.markers = [];
			this.complaints.push(complaint);
		},

		setupIcons() {
			this.iconCobrador = L.icon({
				iconUrl: '/images/sem-cobrador.png',
				iconSize: [36, 36],
				iconAnchor: [18, 36],
				popupAnchor: [0, -36]
			});
			this.iconComplaint = L.icon({
				iconUrl: '/images/treta.png',
				iconSize: [36, 36],
				iconAnchor: [18, 36],
				popupAnchor: [0, -36]
			});
			this.iconCut = L.icon({
				iconUrl: '/images/corte-linha.png',
				iconSize: [36, 36],
				iconAnchor: [18, 36],
				popupAnchor: [0, -36]
			});
			this.iconDelay = L.icon({
				iconUrl: '/images/icone-atrasos.png',
				iconSize: [36, 36],
				iconAnchor: [18, 36],
				popupAnchor: [0, -36]
			});
		}
	}
}
</script>
