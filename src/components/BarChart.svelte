<script>
    import * as d3 from "d3"
    import {onMount} from "svelte"
	import { scaleLinear } from 'd3-scale';
	import { fade } from 'svelte/transition';
	

    export let streamShow = []
	export let index3; 
	let index3P = index3 ;

	let xTicks = [];
    let yTicks = [];
    let padding = { top: 20, right: 50, bottom: 20, left: 25 };

    $: xTicks = streamShow.map(d => d.year);
    $: yTicks = d3.ticks(0, d3.max(streamShow, d => d.count), 5);

	let width = 800;
	let height = 450;

	const minYear = 2016;

	$: xScale = scaleLinear()
        .domain([minYear, d3.max(xTicks)])
        .range([padding.left, width - padding.right]);

	$: yScale = scaleLinear()
        .domain([0, d3.max(yTicks)])
        .range([height - padding.bottom, padding.top]);

	$: innerWidth = width - (padding.left + padding.right);
	$: barWidth = (innerWidth / xTicks.length)*8;
</script>



<div class="chart" {width} {height} >
	<h2 style="text-align: center;
    font-family: Satoshi-Regular;
    color:white;
	opacity:0.7">Cantidad de producciones en el catálogo de Disney+ (por año)</h2>
    <svg >
        
        <g class="axis y-axis">
            {#each yTicks as tick}
                <g class="tick tick-{tick}" transform="translate(0, {yScale(tick)})">
                    <line x2="100%" />
                    <text y="-4">{tick}</text>
                </g>
            {/each}
        </g>

        
        <g class="axis x-axis">
            {#each streamShow as point, i}
                <g class="tick" transform="translate({xScale(point.year)},{height})">
                    <text x={barWidth / 2} y="-4">{point.year}</text>
                </g>
            {/each}
        </g>

        <g class="bars">
            {#each streamShow as point, i}
                <rect
                    x={xScale(point.year) + 2}
                    y={yScale(point.count)}
                    width={barWidth - 4}
                    height={yScale(0) - yScale(point.count)}
                />
            {/each}
        </g>
		
		<!-- linea 2020 -->
		{#if index3 == 1}
		<g class="line2020" transition:fade>
			<line x1="570" y1="0" x2="570" y2="500" opacity= "{index3 == 1 ? "1" : "0"}" style="stroke:#73FFF7;" />
			<text x="580" y="15" fill="black" opacity= "{index3 == 1 ? "1" : "0"}">Covid-19</text>
		</g>
		{/if}
		
    </svg>
</div>



<style>

	.epigrafe1{
		text-align: left;
		
		
	}

	.epigrafe1{
		text-align: left;
	}


	.chart {
		width: 100%;
		max-width: 850px;
		margin: 0 auto;
		padding: 2cap;
	}

	svg {
		position: relative;
		width: 100%;
		height: 450px;
		margin-bottom: -5px;
		background-color: #12031D;
	}
	.line2020{
		
		stroke-width:2;
		transition: all 0.5s ease-in-out;
		
	}
	.line2020 text{
		fill: #73FFF7;
		font-family: Satoshi-Bold;
	}

	.tick {
		font-family: Helvetica, Arial;
		font-size: 0.725em;
		font-weight: 200;
	}

	.tick line {
		stroke: #e2e2e2;
		stroke-dasharray: 2;
		
	}

	.tick text {
		fill: #ccc;
		text-anchor: start;
		font-family: Satoshi-Regular;
	}

	.tick.tick-0 line {
		stroke-dasharray: 0;
	}

	.x-axis .tick text {
		text-anchor: middle;
	}

	.bars rect {
		fill: #EF33FF;
		stroke: none;
		opacity: 0.9;
		transition: all 0.5s ease-in-out;
	}
</style>
