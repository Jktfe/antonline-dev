<script lang="ts">
	interface Props {
		gradientSize?: number;
		gradientColor?: string;
		gradientOpacity?: number;
		class?: string;
		children?: import('svelte').Snippet;
	}

	let {
		gradientSize = 220,
		gradientColor = 'rgba(255, 255, 255, 0.06)',
		gradientOpacity = 1,
		class: className = '',
		children
	}: Props = $props();

	/* svelte-ignore state_referenced_locally */
	let mouseX = $state(-gradientSize);
	/* svelte-ignore state_referenced_locally */
	let mouseY = $state(-gradientSize);
	let isHovering = $state(false);

	let bg = $derived(
		`radial-gradient(${gradientSize}px circle at ${mouseX}px ${mouseY}px, ${gradientColor}, transparent 100%)`
	);

	function handleMouseEnter() {
		isHovering = true;
	}

	function handleMouseMove(e: MouseEvent) {
		if (!isHovering) return;
		const rect = (e.currentTarget as HTMLDivElement).getBoundingClientRect();
		mouseX = e.clientX - rect.left;
		mouseY = e.clientY - rect.top;
	}

	function handleMouseLeave() {
		isHovering = false;
		mouseX = -gradientSize;
		mouseY = -gradientSize;
	}
</script>

<div
	onmouseenter={handleMouseEnter}
	onmousemove={handleMouseMove}
	onmouseleave={handleMouseLeave}
	class="magic-card {className}"
	role="region"
>
	<div class="magic-card__content">
		{#if children}{@render children()}{/if}
	</div>
	<div
		class="magic-card__spotlight"
		style="background: {bg}; opacity: {isHovering ? gradientOpacity : 0};"
	></div>
</div>

<style>
	.magic-card {
		position: relative;
		overflow: hidden;
	}

	.magic-card__content {
		position: relative;
		z-index: 1;
	}

	.magic-card__spotlight {
		position: absolute;
		inset: 0;
		pointer-events: none;
		transition: opacity 250ms ease;
		z-index: 0;
	}
</style>
