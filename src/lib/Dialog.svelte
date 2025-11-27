<script lang="ts">
	import type { Snippet } from 'svelte';

	interface Props {
		/** Two-way binding for open state */
		open?: boolean;
		/** Whether to open as modal (default) or non-modal */
		modal?: boolean;
		/** Close behavior: 'any' (light dismiss), 'closerequest' (Esc key), 'none' (manual only) */
		closedby?: 'any' | 'closerequest' | 'none';
		/** Return value when dialog closes */
		returnValue?: string;
		/** Dialog content */
		children?: Snippet;
		/** Custom class for the dialog */
		class?: string;
		/** Called when dialog closes */
		onclose?: (e: Event) => void;
		/** Called when dialog is cancelled (Esc key) */
		oncancel?: (e: Event) => void;
	}

	let {
		open = $bindable(false),
		modal = true,
		closedby,
		returnValue = $bindable(''),
		children,
		class: className = '',
		onclose,
		oncancel
	}: Props = $props();

	let dialogEl: HTMLDialogElement | undefined = $state();

	$effect(() => {
		if (!dialogEl) return;

		if (open) {
			if (!dialogEl.open) {
				// eslint-disable-next-line @typescript-eslint/no-unused-expressions
				modal ? dialogEl.showModal() : dialogEl.show();
			}
		} else {
			if (dialogEl.open) {
				dialogEl.close();
			}
		}

		const handleKeydown = (e: KeyboardEvent) => {
			if (e.key === 'Escape' && closedby === 'none') {
				e.preventDefault();
				// works with bubbling, at least on current testing
				// e.stopPropagation();
			}
		};

		// Capture phase to intercept before dialog's internal handler
		dialogEl.addEventListener('keydown', handleKeydown, true);

		return () => dialogEl.removeEventListener('keydown', handleKeydown, true);
	});

	function handleClose(e: Event) {
		open = false;
		returnValue = dialogEl?.returnValue ?? '';
		onclose?.(e);
	}

	function handleCancel(e: Event) {
		if (closedby === 'none') {
			e.preventDefault();
			return;
		}

		oncancel?.(e);
	}

	function handleBackdropClick(e: MouseEvent) {
		if (closedby !== 'any') return;

		if (e.target === dialogEl) dialogEl?.close();
	}

	/** Programmatically open the dialog */
	export function show() {
		open = true;
	}

	/** Programmatically close the dialog */
	export function close(value?: string) {
		if (value !== undefined && dialogEl) {
			dialogEl.close(value);
		} else {
			dialogEl?.close();
		}
	}
</script>

<dialog
	bind:this={dialogEl}
	class="dialog {className}"
	onclick={handleBackdropClick}
	onclose={handleClose}
	oncancel={handleCancel}
>
	{#if children}
		<div class="dialog-content">
			{@render children()}
		</div>
	{/if}
</dialog>

<style>
	.dialog {
		/* Reset & base styles */
		border: none;
		outline: none;
		border-radius: var(--dialog-radius, 0.5rem);
		padding: var(--dialog-padding, 0);
		max-width: var(--dialog-max-width, min(90vw, 32rem));
		max-height: var(--dialog-max-height, 85vh);
		width: fit-content;
		height: fit-content;
		overflow: auto;
		background: var(--dialog-bg, white);
		color: var(--dialog-color, inherit);
		box-shadow: var(
			--dialog-shadow,
			0 25px 50px -12px rgb(0 0 0 / 0.25),
			0 0 0 1px rgb(0 0 0 / 0.05)
		);

		/* Center alignment */
		position: fixed;
		inset: 0;
		margin: auto;
	}

	/* Base open state - always visible (Safari fallback) */
	.dialog:open {
		opacity: 1;
		transform: scale(1);
	}

	/* Backdrop base */
	.dialog::backdrop {
		background-color: var(--dialog-backdrop, rgb(0 0 0 / 0.5));
	}

	/* Animation only for browsers that support @starting-style */
	@supports (selector(:open)) {
		.dialog {
			opacity: 0;
			transform: scale(var(--dialog-scale-from, 0.95));
			transition:
				opacity var(--dialog-duration, 0.2s) var(--dialog-easing, ease-out),
				transform var(--dialog-duration, 0.2s) var(--dialog-easing, ease-out);
		}

		.dialog:open {
			opacity: 1;
			transform: scale(1);
		}

		.dialog::backdrop {
			background-color: transparent;
			transition: background-color var(--dialog-duration, 0.2s) var(--dialog-easing, ease-out);
		}

		.dialog:open::backdrop {
			background-color: var(--dialog-backdrop, rgb(0 0 0 / 0.5));
		}
	}

	/* Entry animation for browsers that support @starting-style */
	@supports selector(:open) {
		@starting-style {
			.dialog:open {
				opacity: 0;
				transform: scale(var(--dialog-scale-from, 0.95));
			}

			.dialog:open::backdrop {
				background-color: transparent;
			}
		}
	}

	/* Exit animation for browsers that support allow-discrete */
	@supports (transition-behavior: allow-discrete) {
		.dialog {
			transition:
				opacity var(--dialog-duration, 0.2s) var(--dialog-easing, ease-out),
				transform var(--dialog-duration, 0.2s) var(--dialog-easing, ease-out),
				overlay var(--dialog-duration, 0.2s) var(--dialog-easing, ease-out) allow-discrete,
				display var(--dialog-duration, 0.2s) var(--dialog-easing, ease-out) allow-discrete;
		}

		.dialog::backdrop {
			transition:
				background-color var(--dialog-duration, 0.2s) var(--dialog-easing, ease-out),
				overlay var(--dialog-duration, 0.2s) allow-discrete,
				display var(--dialog-duration, 0.2s) allow-discrete;
		}
	}

	.dialog-content {
		padding: var(--dialog-content-padding, 1.5rem);
	}
</style>
