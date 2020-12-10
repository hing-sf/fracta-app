<template>
	<div id="map">
		<div class="action-container">
			<button type="button" @click="setTargetLayer('year')">show by Years</button>
			<button type="button" @click="setTargetLayer('diameter')">show by Diameter</button>
			<button type="button" @click="setTargetLayer('material')">show by Material</button>
			<button type="button" @click="updateRandomYear('year')">Show by Random Year</button>
			<button type="button" @click="setTargetLayer('')">CLEAR</button>
		</div>
		<div class="map">
			<MglMap :accessToken="accessToken" :mapStyle="mapStyle" :center="center" :zoom="zoom" @load="onMapboxLoaded">
				<div>
					<div v-for="layer in layerList" :key="layer[1].id">
						<MglGeojsonLayer :sourceId="layer[0].data.id" :source="layer[0]" :layerId="layer[1].id" :layer="layer[1]" />
					</div>
				</div>
			</MglMap>
		</div>
	</div>
</template>

<script>
import Mapbox from 'mapbox-gl';
import { MglMap, MglGeojsonLayer } from 'vue-mapbox';

export default {
	components: {
		MglMap,
		MglGeojsonLayer
	},
	props: ['geoJsonList', 'randomYear', 'setCenter', 'setZoom', 'setStyle'],
	data() {
		return {
			accessToken: 'pk.eyJ1IjoiZnJhY3RhdGVzdHMiLCJhIjoiY2tnaWd3NW10MDAzNDJzcnJuNzVrNGd5cSJ9.6pV_QFxNJE22pj4-uKCjpQ',
			mapStyle: this.setStyle,
			center: this.setCenter,
			zoom: this.setZoom,
			activeLayer: '',
			layerList: {}
		};
	},
	created() {
		this.mapbox = Mapbox;
		this.map = null;
	},
	methods: {
		onMapboxLoaded({ map }) {
			this.map = map;
		},

		/**
			@description remove existing Layers and set targeted Layer
 		*/
		setTargetLayer(target) {
			if (this.activeLayer && target !== this.activeLayer) {
				for (const layer of this.geoJsonList[this.activeLayer]) {
					const sourceId = layer[0].data.id;
					const layerId = layer[1].id;

					try {
						this.map.removeLayer(layerId);
						this.map.removeSource(sourceId);
					} catch (err) {
						console.log('err', err);
					}
				}
			}

			this.activeLayer = target;
			this.layerList = this.geoJsonList[target];
		},

		/**
			@description call randomYear() from parent component to update random year.
			@description set updated year data as new Layer
 		*/
		updateRandomYear(target) {
			this.$emit('randomYear');
			this.setTargetLayer(target);
		}
	}
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.map {
	height: 900px;
	width: 100%;
}

.action-container {
	display: flex;
	justify-content: space-evenly;
	margin: 20px auto;
	width: 600px;
}
</style>
