<template>
	<div v-if="!hidden" class="splash-screen dark" :class="{ 'fade-out': doneLoading }">
		<div v-if="os !== 'MacOS'" class="app-buttons">
			<button
				class="btn icon-only transparent"
				icon-only
				@click="() => getCurrentWindow().minimize()"
			>
				<MinimizeIcon />
			</button>
			<button class="btn icon-only transparent" @click="() => getCurrentWindow().toggleMaximize()">
				<MaximizeIcon />
			</button>
			<button class="btn icon-only transparent" @click="handleClose">
				<XIcon />
			</button>
		</div>
		<div class="app-logo-wrapper" data-tauri-drag-region>
			<img :src="dragonLogo" alt="Dragon Launcher" class="dragon-logo" />
		</div>
		<div class="black-bg" data-tauri-drag-region></div>
	</div>
</template>

<script setup>
import { MaximizeIcon, MinimizeIcon, XIcon } from '@modrinth/assets'
import { getCurrentWindow } from '@tauri-apps/api/window'
import { ref, watch } from 'vue'

import dragonLogo from '@/assets/dragon.jpg'
import { getOS } from '@/helpers/utils.js'
import { useLoading } from '@/store/loading.js'

const doneLoading = ref(false)
const hidden = ref(false)

const loading = useLoading()

watch(loading, (newValue) => {
	if (!newValue.barEnabled) {
		if (!loading.loading) {
			doneLoading.value = true

			setTimeout(() => {
				hidden.value = true
				loading.setEnabled(true)
			}, 50)
		}
	}
})

const os = ref('')
getOS().then((x) => (os.value = x))

const handleClose = async () => {
	await getCurrentWindow().close()
}
</script>

<style scoped lang="scss">
.splash-screen {
	transition: opacity 0.25s ease-in-out;
	opacity: 1;

	&.fade-out {
		opacity: 0;
	}
}

.app-buttons {
	position: absolute;
	right: 0;
	z-index: 9999;
	display: flex;
}

.app-logo-wrapper {
	position: absolute;
	height: 100vh;
	width: 100%;

	display: flex;
	flex-direction: column;
	justify-content: center;
	align-items: center;

	z-index: 9998;
}

.dragon-logo {
	height: 10rem;
	width: auto;
	object-fit: contain;
}

.black-bg {
	position: absolute;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
	background: #000000;
	z-index: 9995;
}
</style>
