<script>
    export let yScale, groupByOrg; // y axis can be organized by continent or the color groupings   
    $: ticks = yScale.domain();
    import {fade} from "svelte/transition"
</script>

<g class = "axis y">
    {#each ticks as tick, index}  <!-- loop through ticks -->
        {#if groupByOrg}
            <g class="tick" in:fade={{delay: index*100}} out:fade={{duration:200}}> 
                <text y={yScale(tick)}> {tick} </text>
            </g>
        {/if}
    {/each}
</g>