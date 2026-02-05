<template>
	<img :src="quiltIcon" alt="Quilt" class="loader-icon" />
</template>

<script setup lang="ts">
import quiltIcon from './quilt.png'
</script>

<style scoped>
.loader-icon {
	width: 100%;
	height: 100%;
	object-fit: contain;
}
</style>
