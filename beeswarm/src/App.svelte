<script>
  import data from "$data/data.js";
  //console.log(data)
  //import * as d3 from 'd3';
  //console.log("D3 Object: ")
  //console.log(d3);
  import { forceSimulation, 
    forceX, 
    forceY, 
    forceCollide } from "d3-force";
  import { scaleLinear, 
    scaleBand, 
    scaleOrdinal, 
    scaleSqrt } from "d3-scale";
  
  import AxisX from "$components/AxisX.svelte";
  import AxisY from "$components/AxisY.svelte";
  import Legend from "$components/Legend.svelte";
  import Tooltip from "$components/Tooltip.svelte";

  let width = 400,
    height = 400;
  const margin = { 
    top: 5, 
    right: 5, 
    left: 5, 
    bottom: 45 };

  $: innerWidth = width - margin.left - margin.right; // $ bc it dynamically updates
  let innerHeight = height - margin.top - margin.bottom;
  

  import { group, mean, rollups } from "d3-array";

  // ave for each continent
  const continents = rollups(
    data,
    v => mean(v, d => d.happiness),
    d => d.continent
  ) 
  // group by continent, return group means
    .sort((a, b) => a[1] - b[1]) 
    .map(d => d[0]); 
    
  // Color is based on continent
  const colorRange = [
    "#eb4c42",
    '#f9b9ac',
    '#68b6a3',
    '#a1c181',
    '#e3dac9',
    '#374f2f',
  ]
  let colorScale = scaleOrdinal()
    .domain(continents) //array of continents
    .range(colorRange) 

  $: xScale = scaleLinear()
    .domain([1, 9]) // hard coded 
    .range([0, innerWidth]);


  let yScale = scaleBand()
    .domain(continents) // defined above
    .range([innerHeight, 0])
    .paddingOuter(0.5);
  
  // size of circles
  $: radiusScale = scaleSqrt()
    .domain([1,9])
    .range(width < 568 ? [2,6] : [3,8]); //change size based on width of screen

  let simulation = forceSimulation(data); // instantiates all the dots in top left corner, reactivity issue occurs bc every time width changes, simulation is reinstantiated
  
  let nodes = []; 
  simulation.on('tick', ()=> {
    nodes = simulation.nodes();  // only change whenever simulation ticks
  });

  $: {
      simulation
        .force(
          "x", 
          forceX()
            .x(d => xScale(d.happiness))
            .strength(0.8)
          )
        .force(
          "y", 
          forceY()
            .y(d => (groupByContinent ? yScale(d.continent) : innerHeight/2))    // does group/split based on if we want groupByContinent
            .strength(0.2)
          )
        .force(
          "collide", 
          forceCollide().radius((d) => radiusScale(d.happiness)))

        .alpha(0.3) //speed of updating
        .alphaDecay(0.0005)
        .restart(); // restart simulation
    }

  // Incorporate hovering components. Highlight hovered components only, if a continent is hovered highlight relevant circles only
  let hovered, hoveredContinent;
  import{fade, fly} from "svelte/transition";

  // bool value for if circles are grouped or not
  let groupByContinent = false;
</script>

<!--Create title for chart-->
<h1> The Happiest Countries in the World</h1>

<!-- Hover over a speicifc color in the legend to highlight on the map-->
<Legend {colorScale} bind:hoveredContinent />

<!-- svelte-ignore a11y-click-events-have-key-events -->
<div 
class='chart-container' 
  bind:clientWidth={width}
  on:click = {() => {
    groupByContinent = !groupByContinent; // change bool value
    hovered = null;
  }}
  >
  <svg {width} {height}>
    <!-- Reference line -->
    {#if hovered} 
      <line
        transition:fade
        x1 = {hovered.x}
        x2 = {hovered.x}
        y1 = {height - margin.bottom}
        y2 = {hovered.y + margin.top + radiusScale(hovered.happiness)}
        stroke = {colorScale(hovered.continent)}
        stroke-width = "1"
        />
    {/if}

    <!-- Creating the inner chart / circles-->
    <g 
      class="inner-chart" 
      transform="translate({margin.left}, {margin.top})"
      on:mouseleave = {() => (hovered = null)}
    >
      <!-- create axes -->
      <AxisX {xScale} height = {innerHeight}  width = {innerWidth} />
      <AxisY {yScale} {groupByContinent} />

      {#each nodes as node, ind}
        <!-- svelte-ignore a11y-no-noninteractive-tabindex -->
        <circle 
          in:fade = {{ delay: 200 + 10*ind, duration: 400}}
          cx={node.x} 
          cy={node.y} 
          r={radiusScale(node.happiness)} 
          fill = {colorScale(node.continent)} 
          title = {node.country}
          
          opacity={hovered || hoveredContinent        // set opacity based on hover
            ? hovered === node || hoveredContinent === node.continent
              ? 1
              : 0.2
            : 0.9}
          
          stroke={hovered || hoveredContinent       // set stroke based on hover
            ? hovered === node || hoveredContinent === node.continent
              ? "black"
              : "transparent"
            : "#00000090"}

          on:mouseover = {() => (hovered = node)}
          on:focus = {() => (hovered = node)}
          
          tabindex = "0"
          on:click = {(event) =>  {event.stopImmediatePropagation();}}
          />
      {/each}
    </g>
  </svg>
  {#if hovered} <!-- if true-->
    <Tooltip data = {hovered} {colorScale} {width} />
    {/if}
</div>
<style>
  :global(*) {
    font-family: Inter, -apple-system, system-ui;
    -moz-osx-font-smoothing: grayscale;
  }

  :global(.tick text, .axis-title) {
    font-size: 12px; /* How big our text is */
    font-weight: 400; /* How bold our text is */
    fill: hsla(212, 10%, 53%, 1); /* The color of our text */
    user-select: none; /* Prevents text from being selected */
  }

  h1 {
    margin: 0 0 0.5rem 0;
    font-size: 1.35rem;
    font-weight: 600;
    text-align: center;
  }

  circle {
    transition: stroke 300ms ease, opacity 300ms ease;
    cursor: pointer;
  }
</style>