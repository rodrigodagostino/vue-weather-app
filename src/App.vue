<template>
	<div class="container">
		<SearchForm @setWeatherData="setWeatherData" @setIsFetching="setIsFetching" />
		<transition name="fade-slide-up" leave-active-class="fade" appear>
			<BaseSpinner v-if="isFetching" size="large" />
			<section v-else-if="weatherData" class="weather-data">
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
	</div>
</template>

<script>
import { ref, watch } from 'vue'
import BaseSpinner from './components/BaseSpinner.vue'
import SearchForm from './components/SearchForm.vue'

export default {
	name: 'App',
	components: {
		BaseSpinner,
		SearchForm,
	},
	setup() {
		const weatherData = ref( null )
		const units = ref( 'metric' )
		const timeOfDay = ref( null )
		const location = ref( null )
		const iconClasses = ref( null )
		const temperature = ref( null )
		const maxTemperature = ref( null )
		const minTemperature = ref( null )
		const feelsLike = ref( null )
		const humidity = ref( null )
		const description = ref( null )
		const wind = ref( null )
		const isFetching = ref( false )

		const setWeatherData = data => weatherData.value = data

		const setIsFetching = state => isFetching.value = state

		watch( weatherData, ( weatherData, prevWeatherData ) => {
			if ( !prevWeatherData || weatherData.id !== prevWeatherData.id ) {
				const wd = weatherData
				location.value = `${ wd.name }, ${ wd.sys.country }`
				temperature.value = `${ Math.round( wd.main.temp ) }°C`
				maxTemperature.value = `${ Math.round( wd.main.temp_max ) }°C`
				minTemperature.value = `${ Math.round( wd.main.temp_min ) }°C`
				feelsLike.value = `Feels like ${ Math.round( wd.main.feels_like ) }°C`
				humidity.value = wd.main.humidity
				description.value =
					wd.weather[ 0 ].description.charAt( 0 ).toUpperCase() + wd.weather[ 0 ].description.slice( 1 )
				if ( wd.weather[ 0 ].icon.includes( 'd' ) ) {
					timeOfDay.value = 'day'
				} else {
					timeOfDay.value = 'night'
				}
				wind.value = `${ Math.round( wd.wind.speed ) } km/h`
				iconClasses.value = `icon wi wi-owm-${ timeOfDay.value }-${ wd.weather[ 0 ].id }`
			}
		} )

		return {
			weatherData,
			units,
			timeOfDay,
			location,
			iconClasses,
			temperature,
			maxTemperature,
			minTemperature,
			feelsLike,
			humidity,
			description,
			setWeatherData,
			wind,
			isFetching,
			setIsFetching,
		}
	},
}
</script>

<style lang="scss">
/**
 * Variables
 */
:root {
	--font-main: 'Poppins', Avenir, Helvetica, Arial, sans-serif;

	--cyan-200: #a5f3fc;
	--cyan-300: #67e8f9;
	--cyan-400: #22d3ee;
	--cyan-500: #06b6d4;
	--cyan-600: #0891b2;

	--sky-050: #f0f9ff;
	--sky-100: #e0f2fe;
	--sky-200: #bae6fd;
	--sky-300: #7dd3fc;
	--sky-400: #38bdf8;
	--sky-500: #0ea5e9;
	--sky-600: #0284c7;
	--sky-700: #0369a1;
	--sky-800: #075985;
	--sky-900: #0c4a6e;

	--blue-050: #eff6ff;
	--blue-100: #dbeafe;
	--blue-200: #bfdbfe;
	--blue-300: #93c5fd;
	--blue-400: #60a5fa;
	--blue-500: #3b82f6;
	--blue-600: #2563eb;
	--blue-700: #1d4ed8;
	--blue-800: #1e40af;
	--blue-900: #1e3a8a;

	--white: #f3f3f5;
	--gray-050: #ededf0;
	--gray-100: #e1e1e6;
	--gray-150: #d2d2da;
	--gray-200: #c3c3ce;
	--gray-300: #a4a6b5;
	--gray-400: #86889d;
	--gray-500: #686a84;
	--gray-600: #585a70;
	--gray-650: #515266;
	--gray-700: #494a5c;
	--gray-750: #414253;
	--gray-800: #393a49;
	--gray-850: #31323f;
	--gray-900: #2a2a35;
	--gray-950: #22222b;
	--black: #15151a;
}

/**
 * Reset styles.
 */
html {
	box-sizing: border-box;
	height: 100%;
}

*,
*::before,
*::after {
	/* Inherit box-sizing to make it easier to change the property for components that leverage other behavior; see http://css-tricks.com/inheriting-box-sizing-probably-slightly-better-best-practice/ */
	box-sizing: inherit;
}

/* Reset margins and paddings on most elements */
body,
h1,
h2,
h3,
h4,
h5,
h6,
ul,
ol,
li,
p,
pre,
blockquote,
figure,
hr {
	margin: 0;
	padding: 0;
}

a {
	color: var(--white);
	transition: outline 0.32s ease;
	outline: 1px dotted transparent;
}

a:focus {
	outline: 1px dotted var(--white);
}

/* Reset forms and buttons */
input,
textarea,
select,
button {
	color: inherit;
	font: inherit;
	letter-spacing: inherit;
}

input,
textarea,
button {
	border: 1px solid gray;
}

button {
	border-radius: 0;
	padding: 0.75em 1em;
	background-color: transparent;
}

button * {
	pointer-events: none;
}

/* Easy responsive for media elements */
embed,
iframe,
img,
object,
video {
	display: block;
	max-width: 100%;
}

/* Useful table styles */
table {
	table-layout: fixed;
	width: 100%;
}

/**
 * Base styles.
 */
body {
	font-family: var(--font-main);
	font-size: 1rem;
	color: rgba(255, 255, 255, 0.75);
	background: linear-gradient(180deg, var(--sky-400), var(--blue-500));
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	height: 100%;
	text-align: center;
}

.container {
	max-width: 54rem;
	margin: 4rem auto;
	padding: 1rem;
	position: relative;
}

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
}

.icon,
.description {
	grid-column: 2/3;
}

.icon {
	grid-row: 1/5;
	font-size: 8rem;
	color: rgba(255, 255, 255, 0.95);
	position: relative;

	&:after {
		content: '';
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -48%);
		width: 2.4em;
		height: 2.4em;
		border-radius: 50%;
		background-color: rgba(255, 255, 255, 0.15);
		box-shadow: 0 0 0 1rem rgba(255, 255, 255, 0.05);
		z-index: -1;
	}
}

.description {
	grid-row: 5/6;
}

.location,
.temperature,
.max-and-min-temperature,
.feels-like {
	text-align: right;
	grid-column: 1/2;
}

.temperature {
	font-size: 3rem;
	font-weight: 500;
	line-height: 1;
	color: rgba(255, 255, 255, 0.95);
}

.max-and-min-temperature,
.humidity-and-wind {
	display: flex;
	justify-content: flex-end;

	& > * + * {
		margin-left: 1rem;
	}
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

.humidity-and-wind {
	grid-row: 5/6;
	width: 100%;
}

i + span {
	margin-left: 0.325rem;
}

.fade-slide-up-enter-active {
	animation: fade-slide-up 0.32s ease;
}

.fade-slide-up-leave-active {
	animation: fade-slide-up 0.32s ease reverse;
}

@keyframes fade-slide-up {
	from {
		opacity: 0;
		transform: translate3d(0, 2rem, 0);
	}
	to {
		opacity: 1;
		transform: translate3d(0, 0, 0);
	}
}

.fade-slide-down-enter-active {
	animation: fade-slide-down 0.32s ease;
}

.fade-slide-down-leave-active {
	animation: fade-slide-down 0.32s ease reverse;
}

@keyframes fade-slide-down {
	from {
		opacity: 0;
		transform: translate3d(0, -2rem, 0);
	}
	to {
		opacity: 1;
		transform: translate3d(0, 0, 0);
	}
}

.fade-enter-active {
	animation: fade 0.32s ease;
}

.fade-leave-active {
	animation: fade 0.32s ease reverse;
}

@keyframes fade {
	from {
		opacity: 0;
	}
	to {
		opacity: 1;
	}
}
</style>
