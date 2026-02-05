<script setup>
import { DownloadIcon, XIcon } from '@modrinth/assets'
import { Button, injectNotificationManager } from '@modrinth/ui'
import { ref } from 'vue'

import ModalWrapper from '@/components/ui/modal/ModalWrapper.vue'
import { trackEvent } from '@/helpers/analytics'
import { create_profile_and_install as pack_install } from '@/helpers/pack'

const { handleError } = injectNotificationManager()

const versionId = ref()
const project = ref()
const confirmModal = ref(null)
const installing = ref(false)

const onInstall = ref(() => {})
const onCreateInstance = ref(() => {})

defineExpose({
	show: async (projectVal, versionIdVal, callback, createInstanceCallback) => {
		// Check if user is logged in (either online or offline mode)
		const offlineMode = localStorage.getItem('offlineMode') === 'true'
		const { get_default_user } = await import('@/helpers/auth.js')
		const hasOnlineAccount = await get_default_user().catch(() => null)

		if (!offlineMode && !hasOnlineAccount) {
			const { injectNotificationManager } = await import('@modrinth/ui')
			const { handleError } = injectNotificationManager()
			handleError({
				message:
					'You must sign in to install modpacks. Click the "Playing as" section in the sidebar to sign in or enable offline mode.',
			})
			return
		}

		project.value = projectVal
		versionId.value = versionIdVal
		installing.value = false
		confirmModal.value.show()

		onInstall.value = callback
		onCreateInstance.value = createInstanceCallback

		trackEvent('PackInstallStart')
	},
})

async function install() {
	installing.value = true
	confirmModal.value.hide()

	await pack_install(
		project.value.id,
		versionId.value,
		project.value.title,
		project.value.icon_url,
		onCreateInstance.value,
	).catch(handleError)
	trackEvent('PackInstall', {
		id: project.value.id,
		version_id: versionId.value,
		title: project.value.title,
		source: 'ConfirmModal',
	})

	onInstall.value(versionId.value)
	installing.value = false
}
</script>

<template>
	<ModalWrapper ref="confirmModal" header="Are you sure?" :on-hide="onInstall">
		<div class="modal-body">
			<p>You already have this modpack installed. Are you sure you want to install it again?</p>
			<div class="input-group push-right">
				<Button @click="() => $refs.confirmModal.hide()"><XIcon />Cancel</Button>
				<Button color="primary" :disabled="installing" @click="install()"
					><DownloadIcon /> {{ installing ? 'Installing' : 'Install' }}</Button
				>
			</div>
		</div>
	</ModalWrapper>
</template>

<style lang="scss" scoped>
.modal-body {
	display: flex;
	flex-direction: column;
	gap: 1rem;
}
</style>
