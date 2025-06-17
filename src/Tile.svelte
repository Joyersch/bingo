<script>
    export let activeColor = "red";
    export let text = "";

    let isActive = true;

    function toggleColor() {
        isActive = !isActive;
    }
</script>

<!-- svelte-ignore a11y-click-events-have-key-events -->
<div
    class="tile"
    on:click={toggleColor}
    style="background-color: {isActive ? '' : activeColor};"
>
    {text}
</div>

<style>
    .tile {
        outline: 1px solid var(--tile-border-color); /* ✅ outline instead of border */
        outline-offset: -1px;
        width: 8vw;
        height: 8vw;
        box-sizing: border-box;
        cursor: pointer;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 1.2rem;
        font-weight: bold;
        color: var(--text-color);
        background-color: transparent;
        outline: 1px solid var(--tile-border-color);
        outline-offset: -1px;
        transition: background-color 0.3s;
    }
</style>
