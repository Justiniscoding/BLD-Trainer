<script>
    import {createEventDispatcher} from "svelte"

    export let pair = "aa";
    export let word = "AAH";

    let button;

    let dispatch = createEventDispatcher();

    function removePair(){
        button.parentNode.removeChild(button);
        dispatch("removePair", pair);
    }
</script>

<button bind:this={button} on:click={removePair}>{pair}: {word.slice(0,1).toUpperCase() + word.slice(1)}</button>

<style>
    button{
        padding:0.3rem 0.7rem;
    }

    button:hover{
        text-decoration: line-through;
    }
</style>