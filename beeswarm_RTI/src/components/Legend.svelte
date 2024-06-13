<script>
    export let colorScale, hoveredOrg; 
</script>

<div class ="legend" on:mouseleave={() => (hoveredOrg = null)}>
    {#each colorScale.domain() as Org}  <!-- each color in the domain is a continent-->
        <!-- svelte-ignore a11y-mouse-events-have-key-events -->
        <p class:unhovered = {hoveredOrg && hoveredOrg != Org} on:mouseover={() => (hoveredOrg = Org)}>
            <span style="background: {colorScale(Org)}" />
            {Org} 
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