<template>
	<l-marker :lat-lng="position" :title="title" :draggable="false" :icon="icon">
		<l-popup ref="popup">
			<slot v-if="complaint" name="content">
				{{ complaint.complaint }}
			</slot>
			<slot v-else name="content">
				<div class="complaint-form">
					<div class="field">
						<div class="control has-icons-left">
							<input class="input" type="text" placeholder="Nome" v-model="newComplaint.name">
							<span class="icon is-small is-left">
								<i class="fas fa-user"></i>
							</span>
						</div>
					</div>

					<div class="field">
						<div class="control has-icons-left">
							<input class="input" type="email" placeholder="Email" v-model="newComplaint.email">
							<span class="icon is-small is-left">
								<i class="fas fa-envelope"></i>
							</span>
						</div>
					</div>


					<div class="field">
						<label class="label">Tipo</label>
						<div class="control is-expanded">
							<div class="select">
								<select v-model="newComplaint.type">
									<option value="geral">Tretas</option>
									<option value="atraso">Atrasos</option>
									<option value="corte">Corte de linha</option>
									<option value="cobrador">Sem cobrador</option>
								</select>
							</div>
						</div>
					</div>

					<div class="field">
						<div class="control">
							<textarea class="textarea" placeholder="Descreva a treta" v-model="newComplaint.complaint"></textarea>
						</div>
					</div>

					<div class="field">
						<div class="control is-pulled-right">
							<button :class="{
								'button': true,
								'is-danger': true,
								'is-loading': isLoadingSave
								}" :disabled="isLoadingSave"
								@click="onSave()">
								Enviar
							</button>
						</div>
						<div class="is-clearfix">
						</div>
					</div>
				</div>
			</slot>
		</l-popup>
	</l-marker>
</template>

<script>
import { LMarker, LPopup } from 'vue2-leaflet';
export default {

	components: {
		LMarker,
		LPopup
	},

	props: ['complaint', 'icon', 'position', 'text', 'title'],

	data () {
		return {
			newComplaint: {
				name: '',
				email: '',
				type: 'geral',
				complaint: '',
				position: this.position
			},
			isLoadingSave: false
		}
	},

	mounted() {
	},

	methods: {
		onSave() {
			this.isLoadingSave = true;
			axios.post('/tretas', this.newComplaint)
				.then(response => {
					this.isLoadingSave = false;
					this.$refs.popup.mapObject.remove();
					response.data.position = L.latLng(response.data.latitude, response.data.longitude);
					this.$emit('save', response.data);
				}).catch(error => {
					console.error(error);
					this.isLoadingSave = false;
				});
		}
	}
}
</script>
