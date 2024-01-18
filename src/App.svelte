<main>
    <h1>3x3 BLD Memorization Helper</h1>
    <p>This site will help you create different pairs for memorizing groups of two letters for solving cubes blindfolded. For example, instead of remembering HB, you can remember headbutt or hitbox.</p>
    <section>
        <p>Created Pairs: {donePairCount} / 676</p>
        <p>Current Pair: {pair}</p>
    </section>
    <form on:submit={addPair}>
        <input type="text" name="name" id="pairText" bind:value={pairText} autocomplete="off">
        <button on:click={addPair}>Next</button>
        <button on:click={skipPair}>Skip</button>
    </form>
</main>

<script>
    import { onMount } from "svelte";

    const letters = "abcdefghijklmnopqrstuvwxyz".toUpperCase().split("");

    let pairs = [];

    let pair = "";
    let donePairs = {};
    
    let donePairCount = 0;
    let pairText = "";

    function skipPair(button){
        button.preventDefault();
        pair = randomPair();
    }

    function addPair(form){
        form.preventDefault();
        pairs.splice(pairs.findIndex(el => el == pair), 1);
        donePairs[pair] = pairText;
        donePairCount++;
        pairText = "";
        pair = randomPair();
    }

    onMount(() => {
        letters.forEach(letter => {
            letters.forEach(letter2 => {
                pairs.push(letter.toString().concat(letter2.toString()));
            });
        });
        pair = randomPair();
    });

    function randomPair(){
        return pairs[Math.floor(Math.random() * pairs.length)];
    }
</script>