<script>
  import * as d3 from "d3";
  import { onMount } from 'svelte';
  import { fade } from 'svelte/transition';

  // Receive plot data as prop.
  export let peliculasFlopSucc = [];
  export let index4;

  // The chart dimensions and margins as optional props.
  export let width = 928;
  export let height = 500;
  export let marginTop = 20;
  export let marginRight = 30;
  export let marginBottom = 30;
  export let marginLeft = 40;

  // Debugging: Log peliculasFlopSucc to see its structure
  console.log("peliculasFlopSucc", peliculasFlopSucc);

  let xScale, yScale, flopLine, successLine;
  let xTicks = [];
  let yTicks = [];
  const minYear = 2008;

  $: xTicks = peliculasFlopSucc.map(d => d.Year);
  $: yTicks = d3.ticks(0, d3.max(peliculasFlopSucc, d => d.successCount), 5);

  $: if (peliculasFlopSucc.length) {
    // Create the x (horizontal position) scale.
    xScale = d3.scaleLinear()
      .domain(d3.extent(peliculasFlopSucc, (d) => d.year))
      .range([marginLeft, width - marginRight]);

    // Debugging: Log the xScale domain
    console.log("xScale domain", xScale.domain());

    // Create the y (vertical position) scale.
    yScale = d3.scaleLinear()
      .domain([0, d3.max(peliculasFlopSucc, (d) => Math.max(d.flopCount, d.successCount))])
      .range([height - marginBottom, marginTop]);

    // Debugging: Log the yScale domain
    console.log("yScale domain", yScale.domain());

 

    // Create the line generators.
    flopLine = d3.line()
      .x((d) => xScale((d.year)))
      .y((d) => yScale(d.flopCount));

    successLine = d3.line()
      .x((d) => xScale((d.year)))
      .y((d) => yScale(d.successCount));

    // Debugging: Log the generated paths
    console.log("flopLine path", flopLine(peliculasFlopSucc));
    console.log("successLine path", successLine(peliculasFlopSucc));
  }
</script>

<h2>Cantidad de Fracasos vs Éxitos por Año (Marvel)</h2>

<div class="referencias">
  <p style="color: #EF33FF;">Fracasos</p>
  <p style="color: #73FFF7;">Éxitos</p>

</div>

<div class="chart">
  <svg
  {width}
  {height}
  viewBox="0 0 {width} {height}"
  style:max-width="100%"
  style:height="auto"
>
  {#if peliculasFlopSucc.length}
    <!-- X-Axis -->
    <g transform="translate(0,{height - marginBottom})">
      <line stroke="currentColor" x1={marginLeft - 6} x2={width} />

      {#each peliculasFlopSucc.map(d => d.year) as year}

      <line
          stroke="white"
          stroke-opacity="0.1"
          x1={xScale(new Date(year))}
          x2={xScale(new Date(year))}
          y1={-height + marginBottom + marginTop}
          y2={0}
          />
    <!-- X-Axis Ticks -->
    <line
      stroke="currentColor"
      x1={xScale((year))}
      x2={xScale((year))}
      y1={0}
      y2={6}
    />

    <!-- X-Axis Tick Labels -->
    <text fill="currentColor" text-anchor="middle" x={xScale((year))} y={22}
    opacity="0.9">
      {year}
    </text>
  {/each}
  </g>
      
    
    

    <!-- Y-Axis and Grid Lines -->
    <g transform="translate({marginLeft},0)">
      {#each yScale.ticks() as tick, i}
        <!-- {#if tick !== 0} -->
          <!-- 
            Grid Lines. 
            Note: First line is skipped since the x-axis is already present at 0. 
          -->
          <line
            stroke="white"
            stroke-opacity="0.1"
            x1={0}
            x2={width - marginLeft}
            y1={(yScale(Math.round(tick)))}
            y2={(yScale(Math.round(tick)))}
          />

          <!-- 
            Y-Axis Ticks. 
            Note: First tick is skipped since the x-axis already acts as a tick. 
          -->
          <line
            stroke="white"
            x1={0}
            x2={-6}
            y1={Math.round(yScale(tick))}
            y2={Math.round(yScale(tick))}
          />
        <!-- {/if} -->

        <!-- Y-Axis Tick Labels -->
        <text
          fill="white"
          text-anchor="end"
          dominant-baseline="middle"
          x={-9}
          y={(yScale(Math.round(tick)))}
        >
          {(Math.round(tick))}
        </text>
      {/each}

      <!-- Y-Axis Label -->
      <text fill="white" text-anchor="start" x={-marginLeft} y={15} style="margin-bottom: 2%;">
        
      </text>
    </g>

    <!-- Lines -->
    <path fill="none" stroke="#EF33FF" stroke-width="1.5" d={flopLine(peliculasFlopSucc)} />
    <path fill="none" stroke="#73FFF7" stroke-width="1.5" d={successLine(peliculasFlopSucc)} />
  {/if}

  {#if index4 == 1}
		<g class="line2020" transition:fade>
			

      <rect width="215" height="450" x="40" y="20"   fill-opacity="0.3" fill="#EF33FF"/>
      <text x="90" y="80" color="black" opacity= "{index4 == 1 ? "1" : "0"}">Pre "Era Dorada"</text>

      <rect width="427" height="450" x="255" y="20"   fill-opacity="0.2" fill="#73FFF7"/>
      <text x="400" y="80" color="#73FFF7" opacity= "{index4 == 1 ? "1" : "0"}">"Era Dorada"</text>
			
      <rect width="300" height="450" x="682" y="20"   fill-opacity="0.3" fill="#EF33FF"/>
      <text x="750" y="80" color="black" opacity= "{index4 == 1 ? "1" : "0"}">Post-Pandemia</text>
		</g>
		{/if}

  
</svg>
</div>


<style>

  text {
    font-family: Satoshi-Regular;
    fill: white;
  }
  h2 {
    font-family: Satoshi-Regular;
    color: white;
    text-align: center;
    opacity: 0.8;
  }
  .referencias{
    font-family: Satoshi-Bold;
    display: flex;
    justify-content: center;
    flex-direction: row;
    align-items: center;
  }
  p {
    padding-left: 2%;
    max-width: 850px;
  }
  svg {
    position: relative;
		width: 100%;
		height: 450px;
    font-family: Satoshi-Regular;
		
  }
  .chart{
    width: 100%;
		max-width: 850px;
		margin: 0 auto;
		padding: 2cap;
  }
</style>