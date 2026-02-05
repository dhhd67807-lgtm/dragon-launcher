<template>
	<img :src="forgeIcon" alt="Forge" class="loader-icon" />
</template>

<script setup lang="ts">
import forgeIcon from './forge.png'
</script>

<style scoped>
.loader-icon {
	width: 100%;
	height: 100%;
	object-fit: contain;
}
</style>
