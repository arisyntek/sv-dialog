# Svelte 5 Dialog Component

A lightweight, accessible dialog component using the native HTML `<dialog>` element.

[View Demo](https://svelte.dev/playground/802c430e53474b238eacf6e540d4a6c3?version=latest)

## Installation

```bash
npm i @arisyntek/sv-dialog
```

## Examples Usage

### Confirmation Dialog

```svelte
<script lang="ts">
  import { Dialog } from '@arisyntek/sv-dialog';
  
  let open = $state(false);
  
  function handleConfirm() {
    // do something
    open = false;
  }
</script>

<button onclick={() => open = true}>Open Dialog</button>

<Dialog bind:open closedby="none" --dialog-color="#ad2e24">
  	<form method="dialog">
		<h2>Confirm Delete</h2>
		<p>This action cannot be undone.</p>
		<footer>
			<button onclick={() => open = false} type="reset">Cancel</button>
			<button onclick={handleConfirm} type="submit">Delete</button>
		</footer>
	</form>
</Dialog>
```

### Light Dismiss Dialog

```svelte
<script lang="ts">
  import { Dialog } from '@arisyntek/sv-dialog';
  
  let open = $state(false);
</script>

<button onclick={() => open = true}>Light Dialog</button>

<Dialog bind:open closedby="any">
  <p>Click outside or press Esc to close</p>
</Dialog>
```

### Non-Modal Dialog

```svelte
<script lang="ts">
  import { Dialog } from '@arisyntek/sv-dialog';
  
  let open = $state(false);
</script>

<button onclick={() => open = true}>Open Popup</button>

<Dialog bind:open modal={false}>
  <p>Page remains interactive behind this dialog</p>
</Dialog>
```

## Programmatic Control
Programmatic Control lets you control the dialog via a component reference (`bind:this`) instead of binding the `open` state:

```svelte
<script lang="ts">
  import { Dialog } from '@arisyntek/sv-dialog';
  
  let dialogRef;
</script>

<button onclick={() => dialogRef.show()}>Open</button>
<button onclick={() => dialogRef.close('cancelled')}>Close with value</button>

<Dialog bind:this={dialogRef}>
  Content
</Dialog>
```

## Props

| Prop | Type | Default | Description                                                                                          |
|------|------|---------|------------------------------------------------------------------------------------------------------|
| `open` | `boolean` | `false` | Two-way bindable open state                                                                          |
| `modal` | `boolean` | `true` | Modal (blocks page) or non-modal                                                                     |
| `closedby` | `'any' \| 'closerequest' \| 'none'` | - | `any` — Light dismiss (click outside, Esc key, or button). `closerequest` — Esc key or button only. `none` — Button/programmatic only |
| `returnValue` | `string` | `''` | Bindable return value from forms                                                                     |
| `class` | `string` | `''` | Additional CSS class                                                                                 |
| `onclose` | `(e: Event) => void` | - | Close event handler                                                                                  |
| `oncancel` | `(e: Event) => void` | - | Cancel (Esc) event handler                                                                           |


## Styling

### Method 1: CSS Custom Properties

The component exposes CSS variables for easy customization:

```svelte
<!-- Inline on component -->
<Dialog 
  bind:open
  --dialog-bg="#1e1e1e"
  --dialog-color="white"
  --dialog-radius="1rem"
>
  Dark theme dialog
</Dialog>
```

```css
/* Or globally in your CSS */
:root {
  --dialog-bg: #1e1e1e;
  --dialog-color: #ffffff;
  --dialog-radius: 1rem;
  --dialog-backdrop: rgb(0 0 0 / 0.8);
}
```

### Available CSS Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `--dialog-bg` | `white` | Background color |
| `--dialog-color` | `inherit` | Text color |
| `--dialog-radius` | `0.5rem` | Border radius |
| `--dialog-padding` | `0` | Dialog padding |
| `--dialog-content-padding` | `1.5rem` | Content area padding |
| `--dialog-max-width` | `min(90vw, 32rem)` | Maximum width |
| `--dialog-max-height` | `85vh` | Maximum height |
| `--dialog-shadow` | `0 25px 50px...` | Box shadow |
| `--dialog-backdrop` | `rgb(0 0 0 / 0.5)` | Backdrop color |
| `--dialog-duration` | `0.2s` | Animation duration |
| `--dialog-easing` | `ease-out` | Animation easing |
| `--dialog-scale-from` | `0.95` | Scale animation start |

### Method 2: Custom Class + Global Styles

```svelte
<Dialog bind:open class="my-dialog">
  Content
</Dialog>
```

```css
/* In global CSS or :global() block */
:global(.my-dialog) {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border-radius: 1rem;
}

:global(.my-dialog::backdrop) {
  background: rgb(0 0 0 / 0.8);
  backdrop-filter: blur(4px);
}
```

## Accessibility

- Uses native `<dialog>` element with built-in accessibility
- Modal dialogs trap focus automatically
- Esc key closes modal dialogs by default
- Proper `aria-modal` handling via `showModal()`

## Browser Support

All modern browsers since March 2022 with `<dialog>` element support.
