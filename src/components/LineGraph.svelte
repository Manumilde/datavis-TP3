<script>
    import * as d3 from "d3"
	import { draw } from 'svelte/transition';
	import { fade } from 'svelte/transition';
	import { extent } from 'd3-array';
	import { scaleLinear } from 'd3-scale';
	import { line, curveBasis } from 'd3-shape';

	// props
	export let peliculas = [];
	export let show;
    export let progress;
    export let index;
	
	// scales
	let xScale;
	let yScale;
	let pathLine;
    let condit = show;
    let width = 800;
	let height = 450;
    let rangeYmin;
    let rangeYmax;

    let padding = { top: 20, right: 50, bottom: 20, left: 25 };

    $: xTicks = peliculas.map(d => d.year);
    $: yTicks = d3.ticks(0, d3.max(peliculas, d => d.count), 5);

    $: if (index != 1) {

        rangeYmin=50;
        rangeYmax=0;
        
    }else{

        rangeYmin=55;
        rangeYmax=0;
    }
    

	// function to update scales and pathLine when peliculas changes
	$: if (peliculas) {
		xScale = scaleLinear()
			.domain(extent(peliculas, d => d.Orden))
			.range([10, 90]);

		yScale = scaleLinear()
			.domain(extent(peliculas, d => d.iGross))
			.range([rangeYmin, rangeYmax]);

		pathLine = line()
			.x(d => xScale(d.Orden))
			.y(d => yScale(d.iGross))
			.curve(curveBasis);
	}
</script>

<div class="chart" {width} {height}>
    <svg viewBox="0 0 100 100">

        <g class="xyref">
    
        </g>
        <g class="grafTitle" transition:fade>
            
        </g>
        {#if progress >-0.1 && progress < 0.18}
            <path transition:draw={{duration: 2000}}
                d={pathLine(peliculas)} />
        {/if}
        {#if  progress >0.3 && progress < 0.5}
        
        
            <path class="linea2" transition:draw={{duration: 2000}}
                d={pathLine(peliculas)} />
        {/if}
        {#if  progress >0.55 && progress < 0.8}
        
        
            <path class="linea" transition:draw={{duration: 2000}}
                d={pathLine(peliculas)} />
        {/if}

        {#if  progress >0.85 && progress < 0.999}
        
        
            <path class="linea2" transition:draw={{duration: 2000}}
                d={pathLine(peliculas)} />
        {/if}

        <!-- Lineas y referencias -->
        {#if index == 0 && progress > 0.05 && index!=1 && progress < 0.18}
        <g class="line2020" transition:fade opacity= "{index == 0 ? "1" : "0"}">
            <text x="35" y="10" fill="black" opacity= "{index == 0 ? "0.5" : "0"}" font-size="1.5px" >Recaudación en taquilla por película (Marvel)</text>

            <line x1="0" y1="24" x2="100" y2="24" 
             style="stroke: white;" />
             <text x="0" y="23" fill="black"  font-size="1px" >$500.000.000</text>

            <text x="76" y="10" fill="black" opacity= "{index == 0 ? "1" : "0"}" font-size="1.5px" >Spider-Man 3 (2007)</text>
            <circle class="circ1" cx="81.7" cy="12" r="0.8"></circle>
            <text x="85" y="15" fill="black" opacity= "{index == 0 ? "1" : "0"}" font-size="1.5px" >Iron Man (2008)</text>
            <circle class="circ1" cx="90" cy="18" r="0.8" opacity= "{index == 0 ? "1" : "0"}"></circle>
        </g>
        {/if}

        <!-- Lineas seccion 2 index = 1 -->
        {#if index == 1 && progress > 0.35 && progress < 0.49}
        <g class="line2020" transition:fade opacity= "{index == 1 ? "1" : "0"}">
            <text x="10" y="5" fill="black" opacity="0.5" font-size="1.5px" >Recaudación en taquilla por película (DC)</text>

            <line x1="0" y1="24" x2="100" y2="24" 
             style="stroke:white;" />
             <text x="0" y="23" fill="black"  font-size="1px" >$1.000.000.000</text>

             <line x1="0" y1="37" x2="100" y2="37" 
             style="stroke:white;" />
             <text x="0" y="36" fill="black"  font-size="1px" >$500.000.000</text>

            <text x="50" y="8" fill="black"  font-size="1.5px" >Dark Knight Rises (2012)</text>
            <circle cx="57.6" cy="10" r="0.8"></circle>
            <text x="75" y="13" fill="black"  font-size="1.5px" >Batman v Superman(2016)</text>
            <circle cx="82" cy="15.5" r="0.8" ></circle>
        </g>
        {/if}

        <!-- Lineas seccion 3 index = 2 -->
        {#if progress > 0.6  && index == 2}
        <g class="line2020" transition:fade opacity= "{index == 2 ? "1" : "0"}">

            <text x="35" y="10" fill="black" opacity= "0.5" font-size="1.5px" >Recaudación en taquilla por película (Marvel)</text>

            <line x1="0" y1="41.5" x2="100" y2="41.5" 
            opacity= "{index == 2 ? "1" : "0"}" style="stroke: white;" />
            <line x1="0" y1="20" x2="100" y2="20" 
            opacity= "{index == 2 ? "1" : "0"}" style="stroke: white;" />

            <text x="0" y="19" fill="black" opacity= "{index == 2 ? "1" : "0"}" font-size="1px" >$2.000.000.000</text>
            <text x="0" y="40.5" fill="black" opacity= "{index == 2 ? "1" : "0"}" font-size="1px" >$500.000.000</text>

            <text x="83" y="11" fill="black" opacity= "{index == 2 ? "1" : "0"}" font-size="1.5px" >Endgame (2019)</text>
            <text x="36" y="39" fill="black" opacity= "{index == 2 ? "1" : "0"}" font-size="1.3px" >Iron Man (2008)</text>

            <circle class="circ1" cx="38.3" cy="41" r="0.8"></circle>
            <circle class="circ1" cx="88.8" cy="13.8" r="0.8" ></circle>
        </g>
        {/if}

        {#if progress > 0.87  && index == 3 && progress < 0.999}
        <g class="line2020" transition:fade opacity= "{index == 3 ? "1" : "0"}">

            <text x="35" y="10" fill="black" opacity= "0.5" font-size="1.5px" >Recaudación en taquilla por película (Marvel)</text>

            <line x1="0" y1="36.5" x2="100" y2="36.5" 
             style="stroke: white;" />
            <line x1="0" y1="18" x2="100" y2="18" 
             style="stroke: white;" />

            <text x="0" y="17" fill="black"  font-size="1px" >$2.000.000.000</text>
            <text x="0" y="35.5" fill="black"  font-size="1px" >$500.000.000</text>

            <text x="9" y="10" fill="black"  font-size="1.5px" >Endgame (2019)</text>
            <text x="38" y="24" fill="black"  font-size="1.3px" >Spider-Man: No Way Home (2021)</text>

            <circle  cx="14.5" cy="12.3" r="0.8" ></circle>
            <circle  cx="47.7" cy="26" r="0.8"></circle>
        </g>
        {/if}
        
    
    
    </svg>

</div>

<style>
	path {
		stroke: #EF33FF;
		stroke-width: 0.5;
		fill: none;
		stroke-linecap: round;
        box-shadow: 10px 10px 8px #888888;
        
	}
    .grafTitle {
		transition: all 10s ease-in-out;
        
	}
    .linea2 {
		stroke: #73FFF7;
		stroke-width: 0.5;
		fill: none;
		stroke-linecap: round;
        
	}
    .line2020{
        color: aliceblue;
        
		stroke-dasharray: 1 0.5;
		stroke-width:0.1;
		transition: all 10s ease-in-out;
	}
    text{
        color: #73FFF7;
        fill: white;
    }
    circle{
        fill: #EF33FF;
        
    }
    .circ1{
        fill: #73FFF7;
    }
    text {
        font-family: Satoshi-Medium;
    }
</style>