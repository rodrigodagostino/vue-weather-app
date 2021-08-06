<template>
	<main class="site-main">
		<div class="container">
			<SearchForm @fetchWeatherData="fetchWeatherData" />
			<WeatherDetails
				v-if="weatherData"
				:weatherData="weatherData"
				:isFetching="isFetching"
				:units="units"
				:timeOfDay="timeOfDay"
			/>
		</div>
	</main>
	<footer class="site-footer">
		<div class="container">
			<a href="https://github.com/rodrigodagostino/vue-weather-app" target="_blank">
				Made with <img src="@/assets/vue-logo.png" alt="Vue.js logo" /> by Rodrigo D’Agostino
			</a>
		</div>
	</footer>
</template>

<script>
import { ref, watch } from 'vue'
import SearchForm from './components/SearchForm.vue'
import WeatherDetails from './components/WeatherDetails.vue'

export default {
	name: 'App',
	components: {
		SearchForm,
		WeatherDetails,
	},
	setup() {
		const weatherData = ref( null )
		const apiKey = process.env.VUE_APP_OPENWEATHERMAP_API_KEY
		const isFetching = ref( false )
		const appClasses = ref( 'clear-sky--day' )
		const units = ref( 'metric' )
		const timeOfDay = ref( null )

		// A second fetch is required to access additional data (e.g., sunrise, sunset, timezone).
		const fetchWeatherData = async selectedLocation => {
			try {
				isFetching.value = true
				const url = `https://api.openweathermap.org/data/2.5/weather?q=${ selectedLocation }&appid=${ apiKey }&units=${ units.value }`
				const response = await fetch( url )
				const data = await response.json()
				weatherData.value = data
				isFetching.value = false
			} catch ( error ) {
				console.error( error )
			}
		}

		watch( weatherData, ( weatherData, prevWeatherData ) => {
			if ( !prevWeatherData || weatherData.id !== prevWeatherData.id ) {
				const wd = weatherData
				if ( wd.dt > wd.sys.sunrise && wd.dt < wd.sys.sunset ) {
					timeOfDay.value = 'day'
				} else {
					timeOfDay.value = 'night'
				}

				const app = document.getElementById( 'app' )
				if ( wd.weather[ 0 ].description.includes( 'clear sky' ) ) {
					app.className = `clear-sky--${ timeOfDay.value }`
				}
				if ( wd.weather[ 0 ].description.includes( 'clouds' ) ) {
					app.className = `clouds--${ timeOfDay.value }`
				}
				if ( wd.weather[ 0 ].description.includes( 'rain' ) ) {
					app.className = `rain--${ timeOfDay.value }`
				}
				if ( wd.weather[ 0 ].description.includes( 'drizzle' ) ) {
					app.className = `drizzle--${ timeOfDay.value }`
				}
				if ( wd.weather[ 0 ].description.includes( 'thunderstorm' ) ) {
					app.className = `thunderstorm--${ timeOfDay.value }`
				}
				if ( wd.weather[ 0 ].description.includes( 'snow' ) ) {
					app.className = `snow--${ timeOfDay.value }`
				}
				if ( wd.weather[ 0 ].description.includes( 'mist' ) ) {
					app.className = `mist--${ timeOfDay.value }`
				}
			}
		} )

		return {
			weatherData,
			fetchWeatherData,
			isFetching,
			appClasses,
			units,
			timeOfDay,
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

	--teal-500: #14b8a6;
	--teal-700: #0f766e;

	--sky-400: #38bdf8;
	--sky-600: #0284c7;

	--blue-300: #93c5fd;
	--blue-400: #60a5fa;
	--blue-500: #3b82f6;
	--blue-600: #2563eb;

	--violet-500: #8b5cf6;
	--violet-700: #6d28d9;

	--indigo-300: #a5b4fc;
	--indigo-500: #6366f1;

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
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	height: 100%;
	text-align: center;
}

::selection {
	background-color: rgba(0, 32, 64, 0.12);
}

#app {
	background-color: var(--sky-400);
	width: 100%;
	height: 100%;
	display: flex;
	flex-direction: column;
	justify-content: center;
	position: relative;
	transition: background-color 0.32s ease;

	&[class*='--day'],
	&[class*='--night'] {
		&::before {
			content: '';
			top: 0;
			right: 0;
			bottom: 0;
			left: 0;
			position: fixed;
		}
	}

	&[class*='--night'] {
		.site-footer a {
			color: var(--gray-200);

			&:focus,
			&:hover {
				color: var(--white);
			}
		}
	}

	&[class*='--day']::before {
		background: linear-gradient(172deg, rgba(180, 220, 255, 0.32), transparent, rgba(0, 0, 128, 0.12));
	}

	&[class*='--night']::before {
		background: linear-gradient(172deg, rgba(0, 32, 64, 0.24), transparent, rgba(180, 220, 255, 0.12));
	}

	&.clear-sky--day {
		background-color: var(--sky-400);
	}

	&.clear-sky--night {
		background-color: var(--sky-600);
	}

	&.clouds--day {
		background-color: var(--blue-400);
	}

	&.clouds--night {
		background-color: var(--blue-600);
	}

	&.rain--day,
	&.drizzle--day {
		background-color: var(--teal-500);
	}

	&.rain--night,
	&.drizzle--night {
		background-color: var(--teal-700);
	}

	&.thunderstorm--day {
		background-color: var(--violet-500);
	}

	&.thunderstorm--night {
		background-color: var(--violet-700);
	}

	&.snow--day {
		background-color: var(--blue-300);
	}

	&.snow--night {
		background-color: var(--blue-500);
	}

	&.mist--day {
		background-color: var(--indigo-300);
	}

	&.mist--night {
		background-color: var(--indigo-500);
	}
}

.container {
	max-width: 32rem;
	padding: 1rem;
	margin: 0 auto;
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

.site-footer {
	font-size: 0.875rem;
	text-align: center;
	margin-top: auto;
}

.site-footer .container {
	display: flex;
	justify-content: center;
	padding: 1rem 0;
}

.site-footer a {
	color: var(--gray-700);
	text-decoration: none;
	transition: color 0.24s ease;

	&:focus,
	&:hover {
		color: var(--gray-900);
	}
}

.site-footer img {
	height: 1.25rem;
	width: auto;
	vertical-align: middle;
	margin: 0 0.25rem;
	display: inline-block;
}

@media (min-width: 30rem) {
	.icon,
	.description {
		grid-column: 2;
	}

	.icon {
		grid-row: 1/5;
	}

	.description {
		grid-row: 5;
	}

	.location,
	.temperature,
	.max-and-min-temperature,
	.feels-like {
		text-align: right;
		grid-column: 1;
	}

	.location {
		grid-row: 1;
	}

	.temperature {
		grid-row: 2;
		margin-top: 0;
	}

	.max-and-min-temperature,
	.humidity-and-wind {
		justify-content: flex-end;
	}

	.max-and-min-temperature {
		grid-row: 3;
	}

	.feels-like {
		grid-row: 4;
	}

	.humidity-and-wind {
		grid-row: 5;
	}
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
