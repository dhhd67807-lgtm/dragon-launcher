<template>
	<img :src="vanillaIcon" alt="Vanilla" class="loader-icon" />
</template>

<script setup lang="ts">
import vanillaIcon from './vanilla.png'
</script>

<style scoped>
.loader-icon {
	width: 100%;
	height: 100%;
	object-fit: contain;
}
</style>
