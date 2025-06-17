<script>
    import Tile from "./Tile.svelte";

    export let activeColor = "red";
    export let texts = Array(25).fill("");

    const size = 5;
</script>

<div class="board">
    {#each texts as text, i}
        <Tile {activeColor} {text} />
    {/each}
</div>

<style>
    .board {
        display: grid;
        grid-template-columns: repeat(5, 8vw);
        grid-template-rows: repeat(5, 8vw);
        border: 1px solid var(--tile-border-color); /* ✅ Uses same border color */
        box-sizing: content-box;
    }
</style>
