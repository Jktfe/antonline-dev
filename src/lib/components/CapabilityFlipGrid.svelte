<script lang="ts" module>
  export interface Capability {
    kicker: string;
    title: string;
    desc: string;
    cli?: string;
    docsHref?: string;
  }
</script>

<script lang="ts">
  import { onMount, onDestroy, tick } from 'svelte';

  const { items }: { items: Capability[] } = $props();

  let openIndex = $state<number | null>(null);
  let cardRefs: HTMLElement[] = $state([]);
  let modalRef = $state<HTMLDivElement | null>(null);
  let backdropRef = $state<HTMLDivElement | null>(null);
  let flipReady = $state(false);
  let lastFocus: HTMLElement | null = null;

  let gsapMod: typeof import('gsap') | null = null;
  let flipMod: typeof import('gsap/Flip') | null = null;

  onMount(async () => {
    if (typeof window === 'undefined') return;
    const reduced = window.matchMedia?.('(prefers-reduced-motion: reduce)').matches;
    if (reduced) {
      flipReady = true;
      return;
    }
    gsapMod = await import('gsap');
    flipMod = await import('gsap/Flip');
    gsapMod.gsap.registerPlugin(flipMod.Flip);
    flipReady = true;
  });

  function handleKey(e: KeyboardEvent) {
    if (e.key === 'Escape' && openIndex !== null) {
      e.preventDefault();
      close();
    }
  }

  onMount(() => {
    if (typeof window === 'undefined') return;
    window.addEventListener('keydown', handleKey);
  });

  onDestroy(() => {
    if (typeof window === 'undefined') return;
    window.removeEventListener('keydown', handleKey);
  });

  async function open(i: number) {
    if (openIndex !== null) return;
    lastFocus = document.activeElement as HTMLElement | null;
    const card = cardRefs[i];
    if (!card) return;

    if (gsapMod && flipMod) {
      const state = flipMod.Flip.getState(card);
      openIndex = i;
      await tick();
      if (modalRef) {
        flipMod.Flip.from(state, {
          targets: modalRef,
          duration: 0.55,
          ease: 'power2.inOut',
          absolute: true,
          scale: false
        });
      }
      if (backdropRef) {
        gsapMod.gsap.fromTo(
          backdropRef,
          { autoAlpha: 0 },
          { autoAlpha: 1, duration: 0.3, ease: 'power1.out' }
        );
      }
    } else {
      openIndex = i;
      await tick();
    }

    await tick();
    modalRef?.focus();
  }

  function close() {
    if (openIndex === null) return;
    const closing = openIndex;
    if (gsapMod && flipMod && modalRef && cardRefs[closing]) {
      const state = flipMod.Flip.getState(modalRef);
      openIndex = null;
      tick().then(() => {
        const card = cardRefs[closing];
        if (!card) return;
        flipMod!.Flip.from(state, {
          targets: card,
          duration: 0.45,
          ease: 'power2.inOut',
          absolute: true,
          scale: false
        });
      });
      if (backdropRef) {
        gsapMod.gsap.to(backdropRef, { autoAlpha: 0, duration: 0.25, ease: 'power1.out' });
      }
    } else {
      openIndex = null;
    }
    requestAnimationFrame(() => lastFocus?.focus?.());
  }

  function onBackdropClick(e: MouseEvent) {
    if (e.target === backdropRef) close();
  }

  let active = $derived(openIndex !== null ? items[openIndex] : null);
</script>

<div class="capability-grid">
  {#each items as item, i (item.kicker)}
    <button
      type="button"
      class="capability-card"
      bind:this={cardRefs[i]}
      onclick={() => open(i)}
      aria-haspopup="dialog"
      aria-expanded={openIndex === i}
      data-flip-id="cap-{i}"
    >
      <p class="kicker">{item.kicker}</p>
      <h3>{item.title}</h3>
      <span>{item.desc}</span>
      <span class="cap-hint" aria-hidden="true">More →</span>
    </button>
  {/each}
</div>

{#if openIndex !== null && active}
  <div
    bind:this={backdropRef}
    class="cap-backdrop"
    onclick={onBackdropClick}
    onkeydown={(e) => { if (e.key === 'Escape') close(); }}
    role="presentation"
  >
    <div
      bind:this={modalRef}
      class="cap-modal"
      role="dialog"
      aria-modal="true"
      aria-labelledby="cap-modal-title"
      tabindex="-1"
      data-flip-id="cap-{openIndex}"
    >
      <button type="button" class="cap-close" onclick={close} aria-label="Close">×</button>
      <p class="kicker">{active.kicker}</p>
      <h3 id="cap-modal-title">{active.title}</h3>
      <p class="cap-desc">{active.desc}</p>
      {#if active.cli}
        <pre class="cap-cli"><code>{active.cli}</code></pre>
      {/if}
      {#if active.docsHref}
        <a class="cap-docs" href={active.docsHref}>Read the docs →</a>
      {/if}
    </div>
  </div>
{/if}

<style>
  .capability-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 1.25rem;
  }

  .capability-card {
    display: block;
    text-align: left;
    width: 100%;
    background: rgba(11, 17, 27, 0.78);
    border: 1px solid var(--border, rgba(255, 255, 255, 0.08));
    border-radius: 14px;
    padding: 1.4rem 1.45rem;
    color: inherit;
    cursor: pointer;
    font: inherit;
    transition:
      transform 220ms ease,
      border-color 220ms ease,
      box-shadow 220ms ease;
    position: relative;
    overflow: hidden;
  }

  .capability-card:hover,
  .capability-card:focus-visible {
    transform: translateY(-2px);
    border-color: rgba(34, 197, 94, 0.45);
    box-shadow: 0 18px 36px rgba(0, 0, 0, 0.32);
    outline: none;
  }

  .kicker {
    color: #8EA0B8;
    font-family: var(--font-mono, ui-monospace, monospace);
    font-size: 0.74rem;
    letter-spacing: 0.06em;
    text-transform: uppercase;
    margin: 0 0 0.45rem;
  }

  .capability-card h3,
  .cap-modal h3 {
    margin: 0 0 0.6rem;
    font-size: 1.1rem;
    line-height: 1.3;
    color: var(--text, #E2E8F0);
    font-weight: 600;
  }

  .capability-card span {
    display: block;
    font-size: 0.88rem;
    color: rgba(226, 232, 240, 0.78);
    line-height: 1.55;
  }

  .cap-hint {
    margin-top: 0.85rem;
    font-family: var(--font-mono, ui-monospace, monospace);
    font-size: 0.72rem;
    letter-spacing: 0.06em;
    color: #22C55E;
    opacity: 0.0;
    transform: translateY(4px);
    transition: opacity 220ms ease, transform 220ms ease;
  }

  .capability-card:hover .cap-hint,
  .capability-card:focus-visible .cap-hint {
    opacity: 1;
    transform: translateY(0);
  }

  .cap-backdrop {
    position: fixed;
    inset: 0;
    background: rgba(4, 8, 14, 0.72);
    backdrop-filter: blur(10px);
    -webkit-backdrop-filter: blur(10px);
    z-index: 200;
    display: grid;
    place-items: center;
    padding: 2rem;
  }

  .cap-modal {
    width: min(640px, calc(100vw - 4rem));
    background: rgba(11, 17, 27, 0.96);
    border: 1px solid rgba(34, 197, 94, 0.35);
    border-radius: 16px;
    padding: 2.25rem 2rem;
    box-shadow: 0 40px 80px rgba(0, 0, 0, 0.55);
    color: var(--text, #E2E8F0);
    position: relative;
  }

  .cap-modal h3 {
    font-size: 1.6rem;
  }

  .cap-desc {
    font-size: 1rem;
    line-height: 1.6;
    color: rgba(226, 232, 240, 0.86);
    margin: 0 0 1.25rem;
  }

  .cap-cli {
    background: #060A11;
    border: 1px solid rgba(34, 197, 94, 0.22);
    border-radius: 10px;
    padding: 0.85rem 1rem;
    font-family: var(--font-mono, ui-monospace, monospace);
    font-size: 0.84rem;
    color: #C9F2D8;
    overflow-x: auto;
    margin: 0 0 1.25rem;
  }

  .cap-cli code {
    font: inherit;
    color: inherit;
    white-space: pre;
  }

  .cap-docs {
    display: inline-block;
    color: #22C55E;
    font-family: var(--font-mono, ui-monospace, monospace);
    font-size: 0.84rem;
    letter-spacing: 0.04em;
    text-decoration: none;
  }

  .cap-docs:hover {
    text-decoration: underline;
  }

  .cap-close {
    position: absolute;
    top: 0.85rem;
    right: 1rem;
    background: transparent;
    border: 0;
    color: rgba(226, 232, 240, 0.7);
    font-size: 1.6rem;
    line-height: 1;
    cursor: pointer;
    padding: 0.25rem 0.5rem;
  }

  .cap-close:hover {
    color: #fff;
  }

  @media (max-width: 900px) {
    .capability-grid {
      grid-template-columns: 1fr;
    }
  }
</style>
