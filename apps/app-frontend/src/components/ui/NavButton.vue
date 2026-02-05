<template>
	<RouterLink
		v-if="typeof to === 'string'"
		:to="to"
		v-bind="$attrs"
		:class="{
			'router-link-active': isPrimary && isPrimary(route),
			'subpage-active': isSubpage && isSubpage(route),
			disabled: disabled,
		}"
		class="nav-button w-12 h-12 text-primary rounded-xl flex items-center justify-center text-2xl transition-all bg-transparent hover:text-contrast hover:scale-110"
	>
		<slot />
	</RouterLink>
	<button
		v-else
		v-bind="$attrs"
		class="nav-button button-animation border-none text-primary cursor-pointer w-12 h-12 rounded-xl flex items-center justify-center text-2xl transition-all bg-transparent hover:text-contrast hover:scale-110"
		:disabled="disabled"
		@click="to"
	>
		<slot />
	</button>
</template>

<script setup lang="ts">
import type { RouteLocationNormalizedLoaded } from 'vue-router'
import { RouterLink, useRoute } from 'vue-router'

const route = useRoute()

type RouteFunction = (route: RouteLocationNormalizedLoaded) => boolean

withDefaults(
	defineProps<{
		to: (() => void) | string
		isPrimary?: RouteFunction
		isSubpage?: RouteFunction
		highlightOverride?: boolean
		disabled?: boolean
	}>(),
	{
		disabled: false,
	},
)

defineOptions({
	inheritAttrs: false,
})
</script>

<style lang="scss" scoped>
.nav-button {
	position: relative;
	display: flex !important;
	visibility: visible !important;

	svg {
		transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
	}

	&:hover svg {
		transform: translateX(2px);
	}
}

.router-link-active,
.subpage-active {
	position: relative;

	&::before {
		content: '';
		position: absolute;
		left: -8px;
		top: 50%;
		transform: translateY(-50%);
		width: 3px;
		height: 24px;
		background: white;
		border-radius: 2px;
	}
}

.router-link-active {
	color: white;
}

.subpage-active {
	color: white;

	&::before {
		background: white;
	}
}
</style>
