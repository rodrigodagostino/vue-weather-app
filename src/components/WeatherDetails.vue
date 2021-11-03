<template>
	<transition name="slide-fade-up" leave-active-class="fade" appear>
		<BaseSpinner v-if="isFetching" size="large" />
		<section v-else class="weather-data">
			<h2 class="location">{{ location }}</h2>
			<i :class="iconClasses" />
			<p class="description">{{ description }}</p>
			<p class="temperature">{{ temperature }}</p>
			<div class="max-and-min-temperature">
				<p class="max-temperature">
					<i class="fas fa-chevron-up" />
					<span>{{ maxTemperature }}</span>
				</p>
				<p class="min-temperature">
					<i class="fas fa-chevron-down" />
					<span>{{ minTemperature }}</span>
				</p>
			</div>
			<p class="feels-like">{{ feelsLike }}</p>
			<div class="humidity-and-wind">
				<p class="humidity">
					<i class="wi wi-humidity" />
					<span>{{ humidity }}</span>
				</p>
				<p class="wind">
					<i class="fas fa-wind" />
					<span>{{ wind }}</span>
				</p>
			</div>
		</section>
	</transition>
</template>

<script setup>
import { computed } from 'vue'
import BaseSpinner from '@/components/BaseSpinner.vue'

const props = defineProps({
	weatherData: Object,
	units: String,
	timeOfDay: String,
	isFetching: Boolean,
})

const location = computed( () => `${ props.weatherData.name }, ${ props.weatherData.sys.country }` )
const iconClasses = computed( () => `icon wi wi-owm-${ props.timeOfDay }-${ props.weatherData.weather[ 0 ].id }` )
const temperature = computed( () => `${ Math.round( props.weatherData.main.temp ) }°C` )
const maxTemperature = computed( () => `${ Math.round( props.weatherData.main.temp_max ) }°C` )
const minTemperature = computed( () => `${ Math.round( props.weatherData.main.temp_min ) }°C` )
const feelsLike = computed( () => `Feels like ${ Math.round( props.weatherData.main.feels_like ) }°C` )
const humidity = computed( () => `${ props.weatherData.main.humidity }%` )
const description = computed( () => {
	return (
		props.weatherData.weather[ 0 ].description.charAt( 0 ).toUpperCase() +
				props.weatherData.weather[ 0 ].description.slice( 1 )
	)
})
const wind = computed( () => `${ Math.round( props.weatherData.wind.speed ) } km/h` )
</script>

<style lang="scss">
.weather-data {
	display: grid;
	justify-content: center;
	grid-auto-columns: max-content;
	column-gap: 2rem;
	row-gap: 0.25rem;
}

.weather-data,
.spinner {
	margin: 4rem auto;
}

.location {
	font-size: 1.25rem;
	font-weight: 500;
	grid-row: 1;
}

.icon {
	grid-row: 2;
	font-size: 8rem;
	color: rgba(255, 255, 255, 0.95);
	position: relative;

	&:after {
		content: '';
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -48%);
		width: 2.2em;
		height: 2.2em;
		border-radius: 50%;
		background-color: rgba(255, 255, 255, 0.15);
		box-shadow: 0 0 0 1rem rgba(255, 255, 255, 0.05);
		z-index: 0;
	}
}

.description {
	grid-row: 3;
}

.temperature {
	grid-row: 4;
	font-size: 3rem;
	font-weight: 500;
	color: rgba(255, 255, 255, 0.95);
	line-height: 1;
	margin-top: 1rem;
}

.max-and-min-temperature,
.humidity-and-wind {
	display: flex;
	justify-content: center;

	& > * + * {
		margin-left: 1rem;
	}
}

.max-and-min-temperature {
	grid-row: 5;
}

.max-temperature,
.min-temperature,
.humidity,
.wind {
	display: flex;
	align-items: center;
}

.max-temperature i,
.min-temperature i {
	font-size: 0.75em;
}

.feels-like {
	grid-row: 6;
}

.humidity-and-wind {
	grid-row: 7;
}

i + span {
	margin-left: 0.325rem;
}
</style>
