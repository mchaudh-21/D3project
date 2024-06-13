<script>
    export let colorScale, hoveredContinent; 
</script>

<div class ="legend" on:mouseleave={() => (hoveredContinent = null)}>
    {#each colorScale.domain() as continent}  <!-- each color in the domain is a continent-->
        <!-- svelte-ignore a11y-mouse-events-have-key-events -->
        <p class:unhovered = {hoveredContinent && hoveredContinent != continent} on:mouseover={() => (hoveredContinent = continent)}>
            <span style="background: {colorScale(continent)}" />
            {continent} 
            <p2></p2>  <!-- adding white space in legend -->
          </p>
        {/each}
</div>

<style> 
    .legend{
        display: flex; 
        place-items: center; 
        justify-content: center;
        flex-direction: row;
        flex-wrap: wrap;
        column-gap: 8 px;
        row-gap: 3 px;
        margin-bottom: 0.25rem;
    }

    p {
        margin: 1;
        font-size: 0.8rem;
        text-transform: capitalize;
        cursor: pointer;
        transition: opacity 400ms ease;
        display: flex;
        place-items: center;
        column-gap: 6px;
    }
    
    p.unhovered {
        opacity: 0.2;
    }

    span{
        width: 8px; 
        height: 8px;
        display: inline-block;
        border-radius: 50%;
        border: 1.5px solid rgba(14, 14, 16, 0.537)}

    p2 {
        color: white;
    }
</style>