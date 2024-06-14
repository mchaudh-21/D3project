<script>
    export let xScale, groupByOrg; // y axis can be organized by continent or the color groupings   
    $: ticks = xScale.domain();
    import {fade} from "svelte/transition"
</script>

<g class = "axis x">
    {#each ticks as tick, index}  <!-- loop through ticks -->
        {#if groupByOrg}
            <g class="tick" in:fade={{delay: index*100}} out:fade={{duration:200}}> 
                <text x={xScale(tick)}> CC: {tick} </text>
            </g>
        {/if}
    {/each}
</g>