<template>
	<ModalWrapper ref="modal" header="Sign in to Minecraft">
		<div class="modal-body">
			<div class="login-options">
				<div class="option-card microsoft-option" @click="loginWithMicrosoft">
					<div class="option-icon">
						<svg
							xmlns="http://www.w3.org/2000/svg"
							width="48"
							height="48"
							viewBox="0 0 23 23"
							fill="none"
						>
							<path d="M0 0h11v11H0z" fill="#f25022" />
							<path d="M12 0h11v11H12z" fill="#7fba00" />
							<path d="M0 12h11v11H0z" fill="#00a4ef" />
							<path d="M12 12h11v11H12z" fill="#ffb900" />
						</svg>
					</div>
					<h3>Microsoft Account</h3>
					<p>Sign in with your Microsoft account to access online features and multiplayer</p>
					<ButtonStyled color="primary" size="large" class="w-full">
						<button :disabled="loading">
							<LogInIcon v-if="!loading" />
							<SpinnerIcon v-else class="animate-spin" />
							Sign in with Microsoft
						</button>
					</ButtonStyled>
				</div>

				<div class="divider">
					<span>OR</span>
				</div>

				<div class="option-card offline-option" @click="handleOfflineClick">
					<div class="option-icon">
						<svg
							xmlns="http://www.w3.org/2000/svg"
							width="48"
							height="48"
							viewBox="0 0 24 24"
							fill="none"
							stroke="currentColor"
							stroke-width="2"
							stroke-linecap="round"
							stroke-linejoin="round"
						>
							<path d="M12 2v4" />
							<path d="m16.2 7.8 2.9-2.9" />
							<path d="M18 12h4" />
							<path d="m16.2 16.2 2.9 2.9" />
							<path d="M12 18v4" />
							<path d="m4.9 19.1 2.9-2.9" />
							<path d="M2 12h4" />
							<path d="m4.9 4.9 2.9 2.9" />
						</svg>
					</div>
					<h3>Offline Mode</h3>
					<p>Play without authentication. Perfect for offline or cracked gameplay</p>

					<ButtonStyled color="secondary" size="large" class="w-full">
						<button>
							<UserIcon />
							Play Offline
						</button>
					</ButtonStyled>
				</div>
			</div>
		</div>
	</ModalWrapper>
</template>

<script setup>
import { CheckIcon, LogInIcon, SpinnerIcon, XIcon } from '@modrinth/assets'
import { ButtonStyled, injectNotificationManager } from '@modrinth/ui'
import { UserIcon } from 'lucide-vue-next'
import { ref } from 'vue'

import ModalWrapper from '@/components/ui/modal/ModalWrapper.vue'
import { trackEvent } from '@/helpers/analytics'
import { login as login_flow, set_default_user } from '@/helpers/auth.js'
import { handleSevereError } from '@/store/error.js'

const { handleError } = injectNotificationManager()

const modal = ref()
const loading = ref(false)
const showUsernameInput = ref(false)
const offlineUsername = ref('Player')

const emit = defineEmits(['login-success', 'offline-enabled'])

defineExpose({
	show() {
		showUsernameInput.value = false
		offlineUsername.value = 'Player'
		loading.value = false
		modal.value.show()
	},
	hide() {
		modal.value.hide()
	},
})

async function loginWithMicrosoft() {
	try {
		loading.value = true
		const loggedIn = await login_flow()

		if (loggedIn) {
			await set_default_user(loggedIn.profile.id).catch(handleError)
			emit('login-success', loggedIn)
			trackEvent('AccountLogIn', { source: 'LoginOptionsModal', method: 'microsoft' })
			modal.value.hide()
		}
	} catch (err) {
		handleSevereError(err)
	} finally {
		loading.value = false
	}
}

function handleOfflineClick() {
	loginOffline()
}

function loginOffline() {
	const username = offlineUsername.value.trim() || 'Player'

	// Store offline mode with custom username
	localStorage.setItem('offlineMode', 'true')
	localStorage.setItem('offlineUsername', username)

	emit('offline-enabled', username)
	trackEvent('AccountLogIn', { source: 'LoginOptionsModal', method: 'offline' })
	modal.value.hide()
}
</script>

<style scoped lang="scss">
.modal-body {
	padding: 1.5rem;
	min-width: 600px;
	max-width: 650px;
	position: relative;
}

.login-options {
	display: flex;
	flex-direction: column;
	gap: 1rem;
	position: relative;
	z-index: 1;
}

.option-card {
	display: flex;
	flex-direction: column;
	align-items: center;
	gap: 0.75rem;
	padding: 1.25rem 1.5rem;
	border-radius: var(--radius-lg);
	border: 2px solid var(--color-divider);
	transition: all 0.2s ease;
	cursor: pointer;

	&:hover {
		border-color: var(--color-brand);
		background: var(--color-brand-highlight);
		transform: translateY(-2px);
		box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
	}

	&.microsoft-option {
		// Specific styles for Microsoft if needed
	}

	&.offline-option {
		background: var(--color-bg);
	}
}

.option-icon {
	display: flex;
	align-items: center;
	justify-content: center;
	width: 48px;
	height: 48px;
	border-radius: var(--radius-md);
	background: var(--color-raised-bg);

	svg {
		width: 36px;
		height: 36px;
	}
}

.microsoft-option .option-icon {
	background: white;
	padding: 0.375rem;
}

.offline-option .option-icon {
	color: var(--color-orange);
}

h3 {
	margin: 0;
	font-size: 1.125rem;
	font-weight: 700;
	text-align: center;
}

p {
	margin: 0;
	text-align: center;
	color: var(--color-secondary);
	font-size: 0.8125rem;
	line-height: 1.3;
}

.divider {
	display: flex;
	align-items: center;
	gap: 1rem;
	color: var(--color-secondary);
	font-weight: 600;
	font-size: 0.8125rem;
	margin: 0.25rem 0;

	&::before,
	&::after {
		content: '';
		flex: 1;
		height: 1px;
		background: var(--color-divider);
	}
}

.username-input-section {
	width: 100%;
	display: flex;
	flex-direction: column;
	gap: 0.625rem;
}

.input-field {
	width: 100%;
	padding: 0.625rem 0.875rem;
	border-radius: var(--radius-md);
	border: 2px solid var(--color-divider);
	background: var(--color-raised-bg);
	color: var(--color-contrast);
	font-size: 0.9375rem;
	transition: all 0.2s ease;

	&:focus {
		outline: none;
		border-color: var(--color-brand);
		background: var(--color-bg);
	}

	&::placeholder {
		color: var(--color-secondary);
	}
}

.button-group {
	display: flex;
	gap: 0.5rem;
}
</style>
