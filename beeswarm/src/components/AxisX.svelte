<script>
    export let xScale, width, height;
    $: ticks = xScale.ticks(4); //returns array of values from scale's domain
</script>

<!-- <g> tag is used to group other SVG elements-->
<g class = "axis x">
    {#each ticks as tick}  <!-- loop through ticks -->
        <g class = "tick" transform = "translate({xScale(tick)},0)"> 
            <text x = "5" y = {height}> {tick} </text>
            <line 
                x1 = "0" 
                x2 = "0"
                y1 = "0"
                y2 = {height}
                stroke = "hsla(289, 75for%, 9% 1)" />
        </g>
    {/each}
    <text class = "axis-title" 
        x = {width - innerWidth/2 + 75} 
        y = {height}
        dy = 7
        dominant-baseline = "hanging"
        text-anchor = "end"
        >
        
        Happiness out of 10

    </text>
</g>