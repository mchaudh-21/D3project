<script>
    export let xScale, width, height;
    $: ticks = xScale.ticks(12); //returns array of values from scale's domain
</script>

<!-- <g> tag is used to group other SVG elements-->
<g class = "axis x">
    {#each ticks as tick}  <!-- loop through ticks -->
        <g class = "tick" transform = "translate({xScale(tick)},0)"> 
            <text x = "5" y = {height}> 
                {tick/100}  
            </text>
            <line 
                x1 = "0" 
                x2 = "0"
                y1 = "0"
                y2 = {height}
                stroke = "hsla(289, 75%, 9% 1)" />
        </g>
    {/each}
    <text class = "axis-title" 
        x = {width - innerWidth/2 + 100} 
        y = {height}
        dy = 15
        dominant-baseline = "hanging"
        text-anchor = "end"
        >
        
        Cost in Hundreds of Dollars

    </text>
</g>