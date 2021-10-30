<template>
	<transition name="slide-fade-up" appear>
		<form @submit.prevent="fetchSearchData" class="search-form" :style="formStyles">
			<input
				type="text"
				v-model.trim="query"
				@input="fetchSearchData"
				class="search-input"
				:class="{ 'is-active': isFetching || suggestions }"
				placeholder="Type in your desired location…"
				autofocus
			/>
			<transition name="slide-fade-down" leave-active-class="fade-leave-active" appear mode="out-in">
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
	</transition>
</template>

<script setup>
import { ref, watch, computed } from 'vue'
import _debounce from 'lodash/debounce'

const emit = defineEmits( [ 'fetchWeatherData' ] )

const query = ref( '' )
const apiKey = import.meta.env.VITE_OPENWEATHERMAP_API_KEY
const units = ref( 'metric' )
const suggestions = ref( null )
const selectedLocation = ref( null )
const isFetching = ref( false )

const formStyles = computed( () => {
	if ( !selectedLocation.value ) {
		return { 'margin-top': '32vh' }
	}
	return { 'margin-top': '20vh' }
} )

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

watch( selectedLocation, async ( currSelectedLocation, prevSelectedLocation ) => {
	if ( !prevSelectedLocation || currSelectedLocation !== prevSelectedLocation ) {
		emit( 'fetchWeatherData', currSelectedLocation )
	}
} )

const clearSuggestions = () => suggestions.value = null
</script>

<style lang="scss">
.search-form {
	position: relative;
	margin: 0 auto;
	z-index: 10;
	transition: margin 0.32s ease;
}

.search-input {
	font-size: 1.5rem;
	color: var(--gray-700);
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
		color: var(--gray-700);

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
