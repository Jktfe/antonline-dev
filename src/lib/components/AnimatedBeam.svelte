<script lang="ts" module>
	export interface BeamNode {
		id: string;
		x: number;
		y: number;
		label?: string;
	}

	export interface BeamConnection {
		from: string;
		to: string;
		bidirectional?: boolean;
	}

	export interface AnimatedBeamProps {
		width?: number;
		height?: number;
		nodes: BeamNode[];
		connections: BeamConnection[];
		beamColor?: string;
		beamWidth?: number;
		beamSpeed?: number;
		bidirectional?: boolean;
		gradient?: boolean;
		nodeSize?: number;
		nodeColor?: string;
		nodeStroke?: string;
		labelColor?: string;
	}
</script>

<script lang="ts">
	let {
		width = 600,
		height = 400,
		nodes,
		connections,
		beamColor = '#22C55E',
		beamWidth = 2,
		beamSpeed = 2,
		bidirectional = false,
		gradient = false,
		nodeSize = 12,
		nodeColor = '#0D1520',
		nodeStroke = '#22C55E',
		labelColor = '#9AA8BA'
	}: AnimatedBeamProps = $props();

	let beamPaths = $derived(
		connections
			.map((conn) => {
				const sourceNode = nodes.find((n) => n.id === conn.from);
				const targetNode = nodes.find((n) => n.id === conn.to);
				if (!sourceNode || !targetNode) return null;
				return {
					x1: sourceNode.x,
					y1: sourceNode.y,
					x2: targetNode.x,
					y2: targetNode.y,
					bidirectional: conn.bidirectional ?? bidirectional
				};
			})
			.filter(Boolean) as Array<{
			x1: number;
			y1: number;
			x2: number;
			y2: number;
			bidirectional: boolean;
		}>
	);
</script>

<div
	class="animated-beam-container"
	style="--aspect-ratio: {width / height}; --label-color: {labelColor};"
>
	<svg
		width="100%"
		height="100%"
		viewBox="0 0 {width} {height}"
		preserveAspectRatio="xMidYMid meet"
	>
		{#if gradient}
			<defs>
				<linearGradient id="beam-gradient" gradientUnits="userSpaceOnUse">
					<stop offset="0%" stop-color="transparent" />
					<stop offset="50%" stop-color={beamColor} />
					<stop offset="100%" stop-color="transparent" />
				</linearGradient>
			</defs>
		{/if}

		<g class="beams">
			{#each beamPaths as path (`${path.x1}-${path.y1}-${path.x2}-${path.y2}`)}
				<line
					x1={path.x1}
					y1={path.y1}
					x2={path.x2}
					y2={path.y2}
					stroke={gradient ? 'url(#beam-gradient)' : beamColor}
					stroke-width={beamWidth}
					class="beam {path.bidirectional ? 'bidirectional' : ''}"
					style="--beam-duration: {beamSpeed}s"
					aria-hidden="true"
				/>
			{/each}
		</g>

		<g class="nodes">
			{#each nodes as node (node.id)}
				<circle
					cx={node.x}
					cy={node.y}
					r={nodeSize}
					fill={nodeColor}
					stroke={nodeStroke}
					stroke-width="1.5"
					class="node"
					aria-label={node.label || `Node ${node.id}`}
				/>
				{#if node.label}
					<text
						x={node.x}
						y={node.y - nodeSize - 10}
						text-anchor="middle"
						class="node-label"
					>
						{node.label}
					</text>
				{/if}
			{/each}
		</g>
	</svg>
</div>

<style>
	.animated-beam-container {
		position: relative;
		display: block;
		width: 100%;
		aspect-ratio: var(--aspect-ratio);
	}

	.beam {
		stroke-dasharray: 8 8;
		stroke-linecap: round;
		animation: beam-flow var(--beam-duration, 2s) linear infinite;
	}

	.beam.bidirectional {
		animation: beam-flow-reverse var(--beam-duration, 2s) linear infinite;
	}

	@keyframes beam-flow {
		from { stroke-dashoffset: 100; }
		to { stroke-dashoffset: 0; }
	}

	@keyframes beam-flow-reverse {
		from { stroke-dashoffset: 0; }
		to { stroke-dashoffset: 100; }
	}

	.node {
		filter: drop-shadow(0 2px 4px rgba(0, 0, 0, 0.25));
	}

	.node-label {
		font-family: var(--font-mono, ui-monospace, monospace);
		font-size: 13px;
		font-weight: 500;
		letter-spacing: 0.04em;
		text-transform: uppercase;
		fill: var(--label-color);
		user-select: none;
		pointer-events: none;
	}

	@media (prefers-reduced-motion: reduce) {
		.beam {
			animation: none;
			stroke-dasharray: none;
		}
	}

	@media (max-width: 640px) {
		.node-label {
			font-size: 10px;
		}
	}
</style>
