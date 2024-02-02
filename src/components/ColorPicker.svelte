<script>
    import {primary, accent} from '../utils/colorstore'
</script>

<div class="cpicker">
    <div class="col">
        <h2>Primary Color : </h2>
        <input type="color" bind:value={$primary} >
    </div>
    <div class="col">
        <h2>Accent Color : </h2>
        <input type="color" bind:value={$accent}>
    </div>
</div>

<style>
 .col{
    display: flex;
    align-items: center;
    gap: 1rem;
 }
 .cpicker{
    display: flex;
    flex-wrap: wrap;
    justify-content: space-evenly;
 }
</style>