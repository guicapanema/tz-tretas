<template>

	<nav class="navbar" role="navigation" aria-label="main navigation">
		<div class="navbar-brand">
			<a class="navbar-item" href="/">
				Tretas do Busão
			</a>
		</div>
		<div class="navbar-menu is-flex-mobile is-flex-tablet">
			<div :class="{'control': true, 'is-loading': isLoadingSearch}">
				<input
					class="input is-expanded"
					type="search"
					placeholder="busque um endereço"
					v-model="search"
					@keyup.enter="onSearch()">
			</div>
		</div>
	</nav>

</template>

<script>
export default {

	props: ['isLoadingSearch'],

	data() {
		return {
			search: '',
		}
	},
	
	methods: {
		onSearch() {
			this.$emit('search', this.search);
		}
	}
}
</script>
