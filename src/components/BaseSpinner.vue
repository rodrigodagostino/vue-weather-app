<template>
	<div class="spinner" :class="[ size ? `size-${ size }` : null ]">
		<svg class="circular" viewBox="25 25 50 50">
			<circle
				class="path"
				cx="50"
				cy="50"
				r="20"
				fill="none"
				stroke-width="6"
				stroke-miterlimit="10"
			/>
		</svg>
	</div>
</template>

<script setup>
defineProps({
	size: {
		type: String,
		required: false,
	},
})
</script>

<style>
:root {
	--spinner-size--sm: 24px;
	--spinner-size: 32px;
	--spinner-size--md: 48px;
	--spinner-size--lg: 64px;
}

.spinner {
	position: relative;
	margin: 0 auto;
	width: var(--spinner-size);
}

.spinner .circular {
	animation: rotate 2s linear infinite;
	height: 100%;
	transform-origin: center center;
	width: 100%;
	position: absolute;
	top: 0;
	bottom: 0;
	left: 0;
	right: 0;
	margin: auto;
}

.spinner .path {
	stroke: rgba(255, 255, 255, 0.85);
	stroke-dasharray: 1, 200;
	stroke-dashoffset: 0;
	animation: dash 1.5s ease-in-out infinite;
	/* animation: dash 1.5s ease-in-out infinite, color 6s ease-in-out infinite; */
	stroke-linecap: round;
}

.spinner.size-small {
	width: var(--spinner-size--sm);
}

.spinner.size-medium {
	width: var(--spinner-size--md);
}

.spinner.size-large {
	width: var(--spinner-size--lg);
}

.spinner:before {
	content: '';
	display: block;
	padding-top: 100%;
}

@keyframes rotate {
	100% {
		transform: rotate(360deg);
	}
}

@keyframes dash {
	0% {
		stroke-dasharray: 1, 200;
		stroke-dashoffset: 0;
	}

	50% {
		stroke-dasharray: 89, 200;
		stroke-dashoffset: -35px;
	}

	100% {
		stroke-dasharray: 89, 200;
		stroke-dashoffset: -124px;
	}
}
</style>
