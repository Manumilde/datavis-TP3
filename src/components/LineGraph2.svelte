<script>
	import { draw } from 'svelte/transition';
	import { extent } from 'd3-array';
	import { scaleLinear } from 'd3-scale';
	import { line, curveBasis } from 'd3-shape';
	import { axisBottom, axisLeft } from 'd3-axis';
	import { select } from 'd3-selection';

	// props
	export let peliculas = [];
	export let show;

	// scales
	let xScale;
	let yScale;
	let pathLine;
	let xAxis;
	let yAxis;
	let xAxisGroup;
	let yAxisGroup;

	// function to update scales and pathLine when peliculas changes
	$: if (peliculas) {
		xScale = scaleLinear()
			.domain(extent(peliculas, d => d.Orden))
			.range([10, 90]);

		yScale = scaleLinear()
			.domain(extent(peliculas, d => d.iGross))
			.range([70, 0]);

		pathLine = line()
			.x(d => xScale(d.Orden))
			.y(d => yScale(d.iGross))
			.curve(curveBasis);

		// Create axes
		xAxis = axisBottom(xScale);
		yAxis = axisLeft(yScale);
	}

	// function to render axes
	function renderAxes() {
		if (xAxisGroup) {
			select(xAxisGroup).call(xAxis);
		}
		if (yAxisGroup) {
			select(yAxisGroup).call(yAxis);
		}
	}
</script>

<svg viewBox="0 0 100 100">
	<g bind:this={xAxisGroup} transform="translate(0, 70)"></g>
	<g bind:this={yAxisGroup} transform="translate(10, 0)"></g>
	{#if show}
		<path transition:draw={{duration: 2000}}
			d={pathLine(peliculas)} />
	{/if}
</svg>

<style>
	path {
		stroke: purple;
		stroke-width: 0.5;
		fill: none;
		stroke-linecap: round;
	}
	.axis path,
    .axis line {
		fill: none;
		stroke: #000;
		shape-rendering: crispEdges;
	}
</style>

<svelte:window on:resize={renderAxes} />
