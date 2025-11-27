<script lang="ts">
	import { Dialog } from '$lib';

	let dialogRef;

	let open = $state(false);
	let open1 = $state(false);
	let open2 = $state(false);

	function handleConfirm() {
		// do something
		open = false;
	}
</script>

<div style="padding: 1rem; text-align: center; text-transform: uppercase;">Demo</div>

<button onclick={() => (open = true)}>Open Dialog</button>

<Dialog bind:open closedby="none" --dialog-color="#ad2e24">
	<form method="dialog">
		<h2>Confirm Delete</h2>
		<p>This action cannot be undone.</p>
		<footer>
			<button onclick={() => (open = false)} type="reset">Cancel</button>
			<button onclick={handleConfirm} type="submit">Delete</button>
		</footer>
	</form>
</Dialog>

<button onclick={() => (open1 = true)}>Open Modal</button>

<Dialog bind:open={open1} closedby="any">
	<p>Click outside or press Esc to close</p>
</Dialog>

<button onclick={() => (open2 = !open2)}>Toggle Popup</button>

<Dialog bind:open={open2} modal={false}>
	<p>Page remains interactive behind this dialog</p>
</Dialog>

<button onclick={() => dialogRef.show()}>Open Control</button>

<Dialog bind:this={dialogRef}>
	<p>Programmatic controled</p>
	<button onclick={() => dialogRef.close('cancelled')}>Close Me</button>
</Dialog>
