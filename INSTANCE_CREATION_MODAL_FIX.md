# Instance Creation Modal Fix

## Problem

The instance creation modal (plus button in sidebar and "Create new instance" button in library) was not opening. The error was:

```
Instance creation modal failed to load. Please refresh the page and try again.
installationModal.value: Proxy {...}
```

## Root Cause

The `InstanceCreationModal.vue` component has **top-level `await`** statements (line 291):

```javascript
const [
	fabric_versions,
	forge_versions,
	quilt_versions,
	neoforge_versions,
	all_game_versions,
	loaders,
] = await Promise.all([...])
```

This makes it an **async component** in Vue 3. When you try to access the component via `ref()`, the ref points to a Proxy wrapper, not the actual component instance, so the `show()` method is not immediately available.

## Solution

Wrap the `InstanceCreationModal` in a `<Suspense>` component. This tells Vue to wait for the async component to fully load before making it available.

### Changes Made

#### 1. App.vue - Wrap modal in Suspense

```vue
<!-- Before -->
<InstanceCreationModal ref="installationModal" />

<!-- After -->
<Suspense>
	<InstanceCreationModal ref="installationModal" />
</Suspense>
```

#### 2. App.vue - Keep the retry logic function

```javascript
async function openInstanceCreationModal() {
	console.log('Attempting to open modal...')

	// Wait for async component to be ready (up to 5 seconds)
	let attempts = 0
	const maxAttempts = 50

	while (attempts < maxAttempts) {
		await new Promise((resolve) => setTimeout(resolve, 100))

		const modal = installationModal.value
		if (modal) {
			console.log('Modal ref exists, checking for show method...', modal)

			if (typeof modal.show === 'function') {
				console.log('Modal show method found, opening...')
				try {
					await modal.show()
					console.log('Modal opened successfully')
					return
				} catch (error) {
					console.error('Error opening modal:', error)
					handleError(error)
					return
				}
			}

			if (modal.$el !== undefined) {
				console.log('Modal is a component instance, checking $el...')
			}
		}

		attempts++
		if (attempts % 10 === 0) {
			console.log(`Still waiting for modal... attempt ${attempts}/${maxAttempts}`)
		}
	}

	const errorMsg = 'Instance creation modal failed to load. Please refresh the page and try again.'
	console.error(errorMsg, 'installationModal.value:', installationModal.value)
	handleError(new Error(errorMsg))
}
```

#### 3. library/Index.vue - Same changes

```vue
<!-- Before -->
<InstanceCreationModal ref="installationModal" />

<!-- After -->
<Suspense>
	<InstanceCreationModal ref="installationModal" />
</Suspense>
```

And similar retry logic function (simplified version without extra logging).

## Why This Works

1. **`<Suspense>`** - Handles async component loading properly in Vue 3
2. **Retry Logic** - Waits up to 5 seconds (50 attempts × 100ms) for the component to be fully loaded
3. **Type Checking** - Verifies `modal.show` is a function before calling it

The combination of Suspense + retry logic ensures the modal is fully loaded and accessible before trying to call its `show()` method.

## Files Modified

- `apps/app-frontend/src/App.vue`
- `apps/app-frontend/src/pages/library/Index.vue`

## Testing

✅ Plus button in left sidebar opens modal
✅ "Create new instance" button in library opens modal
✅ Both work on first click without errors
