<script lang="ts" module>
  export interface AntColonyProps {
    width?: number;
    height?: number;
    antSrc?: string;
    antSize?: number;
    antsPerPath?: number;
    speed?: number;
    endpoints?: { id: string; label: string; x: number; y: number }[];
    hueRotations?: number[];
  }
</script>

<script lang="ts">
  import { onMount, onDestroy } from 'svelte';

  const {
    width = 600,
    height = 360,
    antSrc = '/ant.svg',
    antSize = 22,
    antsPerPath = 3,
    speed = 6.5,
    endpoints = [
      { id: 'claude', label: 'Claude', x: 92, y: 68 },
      { id: 'codex', label: 'Codex', x: 510, y: 64 },
      { id: 'gemini', label: 'Gemini', x: 78, y: 296 },
      { id: 'local', label: 'Local', x: 522, y: 300 },
      { id: 'mobile', label: 'ANTios', x: 300, y: 32 }
    ],
    hueRotations = [0, 35, 90, 175, 260]
  }: AntColonyProps = $props();

  // svelte-ignore state_referenced_locally
  const hub = { x: width / 2, y: height / 2 };

  function buildPath(from: { x: number; y: number }, to: { x: number; y: number }, idx: number): string {
    const mx = (from.x + to.x) / 2;
    const my = (from.y + to.y) / 2;
    const dx = to.x - from.x;
    const dy = to.y - from.y;
    const len = Math.hypot(dx, dy) || 1;
    const nx = -dy / len;
    const ny = dx / len;
    const curl = 30 + (idx % 2 === 0 ? 18 : -18);
    const cpx = mx + nx * curl;
    const cpy = my + ny * curl;
    return `M ${from.x},${from.y} Q ${cpx},${cpy} ${to.x},${to.y}`;
  }

  let pathDefs = $derived(
    endpoints.map((e, i) => ({
      id: e.id,
      label: e.label,
      x: e.x,
      y: e.y,
      d: buildPath(e, hub, i)
    }))
  );

  let mounted = $state(false);
  let svgRef = $state<SVGSVGElement | null>(null);
  let tweens: Array<{ kill: () => void }> = [];

  onMount(async () => {
    mounted = true;
    if (typeof window === 'undefined') return;
    const reduced = window.matchMedia?.('(prefers-reduced-motion: reduce)').matches;
    if (reduced) return;

    const { gsap } = await import('gsap');
    const { MotionPathPlugin } = await import('gsap/MotionPathPlugin');
    gsap.registerPlugin(MotionPathPlugin);

    if (!svgRef) return;
    const ants = svgRef.querySelectorAll<SVGImageElement>('[data-ant]');
    ants.forEach((ant) => {
      const pathId = ant.getAttribute('data-path');
      const offset = parseFloat(ant.getAttribute('data-offset') || '0');
      const direction = ant.getAttribute('data-dir') === 'reverse' ? -1 : 1;
      if (!pathId) return;
      const path = svgRef!.querySelector<SVGPathElement>(`#${pathId}`);
      if (!path) return;
      const tween = gsap.to(ant, {
        duration: speed,
        repeat: -1,
        ease: 'none',
        delay: -offset * speed,
        motionPath: {
          path,
          align: path,
          alignOrigin: [0.5, 0.5],
          autoRotate: true,
          start: direction === 1 ? 0 : 1,
          end: direction === 1 ? 1 : 0
        }
      });
      tweens.push(tween);
    });
  });

  onDestroy(() => {
    tweens.forEach((t) => t.kill());
    tweens = [];
  });
</script>

<div
  class="ant-colony"
  style="--aspect-ratio: {width / height}; --ant-size: {antSize}px;"
>
  <svg
    bind:this={svgRef}
    viewBox="0 0 {width} {height}"
    width="100%"
    height="100%"
    preserveAspectRatio="xMidYMid meet"
    aria-hidden="true"
  >
    <defs>
      <radialGradient id="hub-glow" cx="50%" cy="50%" r="50%">
        <stop offset="0%" stop-color="rgba(34,197,94,0.32)" />
        <stop offset="60%" stop-color="rgba(34,197,94,0.08)" />
        <stop offset="100%" stop-color="rgba(34,197,94,0)" />
      </radialGradient>
      {#each hueRotations as hue, i}
        <filter id="ant-hue-{i}" color-interpolation-filters="sRGB">
          <feColorMatrix type="hueRotate" values={String(hue)} />
        </filter>
      {/each}
    </defs>

    <circle cx={hub.x} cy={hub.y} r="78" fill="url(#hub-glow)" />

    {#each pathDefs as p (p.id)}
      <path
        id="path-{p.id}"
        d={p.d}
        fill="none"
        stroke="rgba(148, 163, 184, 0.18)"
        stroke-width="1"
        stroke-dasharray="2 6"
      />
    {/each}

    {#each pathDefs as p, pi (p.id)}
      <g class="endpoint">
        <circle cx={p.x} cy={p.y} r="6" fill="#0D1520" stroke="#22C55E" stroke-width="1.5" />
        <text
          x={p.x}
          y={p.y - 14}
          text-anchor="middle"
          class="endpoint-label"
        >{p.label}</text>
      </g>
      {#each Array(antsPerPath) as _, ai}
        <image
          data-ant
          data-path="path-{p.id}"
          data-offset={ai / antsPerPath}
          data-dir={ai % 2 === 0 ? 'forward' : 'reverse'}
          href={antSrc}
          width={antSize}
          height={antSize}
          x={-antSize / 2}
          y={-antSize / 2}
          filter={`url(#ant-hue-${(pi + ai) % hueRotations.length})`}
          opacity={mounted ? 0.92 : 0}
        />
      {/each}
    {/each}

    <g class="hub">
      <circle cx={hub.x} cy={hub.y} r="38" fill="#0B1320" stroke="#22C55E" stroke-width="1.5" />
      <image
        href={antSrc}
        x={hub.x - 28}
        y={hub.y - 28}
        width="56"
        height="56"
      />
    </g>
  </svg>
</div>

<style>
  .ant-colony {
    position: relative;
    width: 100%;
    aspect-ratio: var(--aspect-ratio);
  }

  .endpoint-label {
    font-family: var(--font-mono, ui-monospace, monospace);
    font-size: 12px;
    font-weight: 500;
    letter-spacing: 0.04em;
    text-transform: uppercase;
    fill: #9AA8BA;
    user-select: none;
  }

  @media (prefers-reduced-motion: reduce) {
    .ant-colony :global([data-ant]) {
      display: none;
    }
  }
</style>
