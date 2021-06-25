<template>
	<form @submit.prevent="fetchSearchData" class="search-form">
		<input
			type="text"
			v-model.trim="query"
			@input="fetchSearchData"
			class="search-input"
			:class="{ 'is-active': isFetching || suggestions }"
			placeholder="Type in your desired location…"
			autofocus
		/>
		<transition name="fade-slide-down" leave-active-class="fade-leave-active" appear mode="out-in">
			<ul v-if="isFetching" class="search-suggestions">
				<li class="search-suggestion">Searching…</li>
			</ul>
			<ul v-else-if="!isFetching && suggestions && suggestions.length > 0" class="search-suggestions">
				<li v-for="suggestion in suggestions" :key="suggestion.id" class="search-suggestion">
					<button type="button" @click="selectLocation(`${suggestion.name}, ${suggestion.sys.country}`)">
						{{ `${suggestion.name}, ${suggestion.sys.country}` }}
					</button>
				</li>
			</ul>
			<ul v-else-if="!isFetching && suggestions && suggestions.length === 0" class="search-suggestions">
				<li class="search-suggestion">There are no matching results.</li>
			</ul>
		</transition>
	</form>
</template>

<script>
import { ref, watch } from 'vue'
import _debounce from 'lodash/debounce'

export default {
	emits: [ 'setWeatherData', 'setIsFetching' ],
	setup( _, context ) {
		const query = ref( '' )
		const apiKey = process.env.VUE_APP_OPENWEATHERMAP_API_KEY
		const units = ref( 'metric' )
		const suggestions = ref( null )
		const selectedLocation = ref( null )
		const isFetching = ref( false )

		const fetchSearchData = _debounce( async () => {
			if ( query.value ) {
				try {
					isFetching.value = true
					const url = `https://api.openweathermap.org/data/2.5/find?q=${ query.value }&appid=${ apiKey }&units=${ units.value }`
					const response = await fetch( url )
					const data = await response.json()
					suggestions.value = data.list
					isFetching.value = false
				} catch ( error ) {
					console.error( error )
				}
			}
		}, 400 )

		const selectLocation = suggestionName => {
			selectedLocation.value = suggestionName
			clearSuggestions()
		}

		// A second fetch is required to get access to additional data (e.g., sunrise, sunset, timezone).
		watch( selectedLocation, async ( currSelectedLocation, prevSelectedLocation ) => {
			if ( !prevSelectedLocation || currSelectedLocation !== prevSelectedLocation ) {
				try {
					context.emit( 'setIsFetching', true )
					const url = `https://api.openweathermap.org/data/2.5/weather?q=${ currSelectedLocation }&appid=${ apiKey }&units=${ units.value }`
					const response = await fetch( url )
					const data = await response.json()
					suggestions.value = data.list
					context.emit( 'setWeatherData', data )
					context.emit( 'setIsFetching', false )
				} catch ( error ) {
					console.error( error )
				}
			}
		} )

		const clearSuggestions = () => suggestions.value = null

		return {
			query,
			suggestions,
			units,
			fetchSearchData,
			selectLocation,
			clearSuggestions,
			isFetching,
		}
	},
}
</script>

<style lang="scss">
.search-form {
	position: relative;
	margin: 0 auto;
	z-index: 10;
}

.search-input {
	font-size: 1.5rem;
	color: var(--gray-600);
	background-color: rgba(255, 255, 255, 0.75);
	border: none;
	border-radius: 2.125rem 2.125rem;
	text-align: center;
	padding: 1rem 1.25rem;
	width: 100%;
	box-shadow: 0 0 0 0.5rem transparent;
	outline: none;
	transition: background-color 0.32s ease, border-radius 0.32s ease, box-shadow 0.32s ease;

	&:focus {
		background-color: rgba(255, 255, 255, 0.9);
		box-shadow: 0 0 0 0.5rem rgba(255, 255, 255, 0.25);
	}

	&.is-active {
		border-radius: 2.125rem 2.125rem 0 0;
	}
}

.search-suggestions {
	list-style: none;
	background-color: rgba(255, 255, 255, 0.75);
	position: absolute;
	top: calc(100% + 0.25rem);
	padding: 1rem 0;
	width: 100%;
	max-width: 32rem;
	border-radius: 0 0 2.125rem 2.125rem;
	overflow: hidden;
	box-shadow: 0 0 0 0.5rem transparent;
	outline: none;
	transition: background-color 0.32s ease, box-shadow 0.32s ease;

	&:focus-within,
	&:hover {
		background-color: rgba(255, 255, 255, 0.9);
	}

	&:focus-within {
		box-shadow: 0 0 0 0.5rem rgba(255, 255, 255, 0.25);
	}

	.search-suggestion {
		color: var(--gray-600);

		button {
			width: 100%;
			padding: 0.5rem 1rem;
			border: none;
			outline: none;
			transition: background-color 0.32s ease;
			cursor: pointer;

			&:focus,
			&:hover {
				background-color: rgba(0, 92, 184, 0.12);
			}
		}
	}
}
</style>
