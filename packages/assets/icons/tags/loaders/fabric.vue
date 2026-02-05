<template>
	<img :src="fabricIcon" alt="Fabric" class="loader-icon" />
</template>

<script setup lang="ts">
import fabricIcon from './fabric.png'
</script>

<style scoped>
.loader-icon {
	width: 100%;
	height: 100%;
	object-fit: contain;
}
</style>
