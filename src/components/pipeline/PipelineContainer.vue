<template>
	<div class="pipelineContainer">
		<Map @randomYear="randomYear" :geo-json-list="geoJsonList" :setCenter="center" :setZoom="zoom" :setStyle="style" />
	</div>
</template>

<script>
import Map from './Map';
import data from '../../data/pipes.json';

export default {
	components: { Map },
	data() {
		return {
			activeLayer: '',
			geoJson: data,
			geoJsonList: {},
			data: data,
			center: [-122.2677, 37.5536],
			zoom: 14,
			style: 'mapbox://styles/mapbox/streets-v11'
		};
	},
	created() {
		this.createData();
	},
	methods: {
		/**
			@description create data object to pass to Map Component
 		*/
		createData() {
			if (!this.data.features) return;

			const data = {
				year: this.formatLayerObj('year'),
				diameter: this.formatLayerObj('diameter'),
				material: this.formatLayerObj('material_type_code')
			};

			this.geoJsonList = data;
		},

		/**
			@description format target item to usable object
 		*/
		formatLayerObj(target) {
			if (!this.data.features) return;

			let parsedData = this.parseObject(target, this.data);
			let geoJsonList = this.createGeoJson(target, parsedData);
			return geoJsonList;
		},

		/**
			@description update a random pipe’s year to a random number between 1960 and 2020.
 		*/
		randomYear() {
			if (!this.data.features) return;

			const len = this.data.features.length;
			const randomEl = this.getRandomNum(len);
			const yearToAdd = 1960;
			const randomYear = this.getRandomNum(60, yearToAdd);

			this.data.features[randomEl].properties.year = randomYear;

			let parsedData = this.parseObject('year', this.geoJson);
			let geoJsonList = this.createGeoJson('year', parsedData);
			this.geoJsonList.year = geoJsonList;
			return;
		},

		/**
			@description create geoJson for selected target
 		*/
		createGeoJson(target, data) {
			let features = [];
			let count = 0;
			const COLOR = ['red', 'blue', 'green', 'orange', 'purple', 'black', 'pink', 'brown', 'maroon'];

			for (const [key, value] of Object.entries(data)) {
				const layer = `${key}-${target}Layer`;

				let geoJson = [
					{
						type: 'geojson',
						data: {
							id: `${key}-${target}`,
							type: 'FeatureCollection',
							features: value
						}
					},
					{
						id: layer,
						type: 'line',
						paint: {
							'line-color': COLOR[count],
							'line-width': 3
						}
					}
				];
				features = [...features, geoJson];
				count++;
			}
			return features;
		},

		/**
			@description parse raw data to usable object
 		*/
		parseObject(target, data) {
			let obj = {};
			for (const el of data.features) {
				let key = el.properties[target];
				if (obj[key]) {
					obj[key] = [...obj[key], el];
				} else {
					obj[key] = [el];
				}
			}
			return obj;
		},

		/**
			@description generate random number or random number between xxxx and xxxx year
  		*/
		getRandomNum(num, year = 0) {
			return Math.floor(Math.random() * Math.floor(num) + year);
		}
	}
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.action-container {
	display: flex;
	justify-content: space-evenly;
	margin: 20px auto;
	width: 600px;
}
</style>
