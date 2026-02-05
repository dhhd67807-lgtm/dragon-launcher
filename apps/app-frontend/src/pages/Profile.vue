<template>
	<div class="profile-page">
		<div class="profile-container">
			<div class="profile-header">
				<h1>Profile</h1>
			</div>

			<div class="profile-content">
				<!-- Minecraft Account Section -->
				<div class="profile-section">
					<h2>Minecraft Account</h2>
					<AccountsCard mode="isolated" />
				</div>
			</div>
		</div>
	</div>
</template>

<script setup>
import AccountsCard from '@/components/ui/AccountsCard.vue'
</script>

<style scoped lang="scss">
.profile-page {
	width: 100%;
	height: 100%;
	overflow-y: auto;
	background: var(--color-bg);
}

.profile-container {
	max-width: 800px;
	margin: 0 auto;
	padding: 2rem;
}

.profile-header {
	margin-bottom: 2rem;

	h1 {
		font-size: 2rem;
		font-weight: 800;
		margin: 0;
		color: var(--color-contrast);
	}
}

.profile-content {
	display: flex;
	flex-direction: column;
	gap: 2rem;
}

.profile-section {
	h2 {
		font-size: 1.25rem;
		font-weight: 700;
		margin: 0 0 1rem 0;
		color: var(--color-contrast);
	}
}
</style>
