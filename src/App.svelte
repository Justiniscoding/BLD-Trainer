<main>
    <h1 id=title>3x3 BLD Memorization Helper</h1>
    <p id=desc>This site will help you create different pairs for memorizing groups of two letters for solving cubes blindfolded. For example, instead of remembering HB, you can remember headbutt or hitbox.</p>
    <section>
        <p>Created Pairs: {donePairCount} / 552</p>
        <p>Current Pair: {pair}</p>
    </section>
    <form on:submit={addPair}>
        <input type="text" name="name" id="pairText" bind:value={pairText} autocomplete="off">
        <button on:click={addPair}>Next</button>
        <button on:click={skipPair}>Skip </button>
    </form>
    <dialog bind:this={startModal} id=modal>
        <p>This site will help you memorize different pairs of letters for solving a Rubik's cube blindfolded. Two letters will correspond to one word, which makes it easier to remember the pairs. To use this site, you first need to create all 676 different pair/word associations. You will then have access to a trainer to learn all of these combinations. You can use press enter to submit the word, and ctrl+enter to skip it(command+enter on mac) </p>
        <button on:click={hideModal}>Ok</button><br>
        <input type="checkbox" name="dontshow" id="noshow" bind:checked={dontShowAgain}>
        <label for="dontshow">Don't show this again</label>
    </dialog>
    <button on:click={resetEverything}>Reset</button>
    <button on:click={exportData}>Export to Anki</button>

    <section>
        <div class="pairs">
            {#each Object.entries(donePairs) as pair}
                <LetterListItem pair={pair[0]} word={pair[1]} on:removePair={removePair}/>
            {/each}
        </div>
    </section>
</main>

<script>
    import LetterListItem from "./lib/LetterListItem.svelte"

    import { onMount } from "svelte";

    const letters = "abcdefghijklmnopqrstuvwx".toUpperCase().split("");

    let pairs = JSON.parse(localStorage.pairs || "[]");

    let dontShowAgain;
    let startModal;

    let pair = "";
    let donePairs = JSON.parse(localStorage.donePairs || "{}");
    
    let donePairCount = 552 - pairs.length;
    let pairText = "";

    function skipPair(button){
        if(button){
            button.preventDefault();
        }
        pair = randomPair();
    }

    function addPair(form){
        form.preventDefault();
        pairs.splice(pairs.findIndex(el => el == pair), 1);
        donePairs[pair] = pairText;
        donePairCount++;
        pairText = "";
        pair = randomPair();
        savePairs();
    }

    function savePairs(){
        localStorage.pairs = JSON.stringify(pairs);
        localStorage.donePairs = JSON.stringify(donePairs);
    }

    onMount(() => {
        if(!localStorage.pairs){
            letters.forEach(letter => {
                letters.forEach(letter2 => {
                    if(letter != letter2){
                        pairs.push(letter.toString().concat(letter2.toString()));
                    }
                });
            });
        }
        pair = randomPair();
        if(!localStorage.dontShow){
            startModal.showModal();
        }
        donePairCount = 552 - pairs.length;

        document.addEventListener("keydown", e => {
            if(e.key == "Enter" && (e.metaKey || e.ctrlKey)){
                skipPair();
            }
        });
    });

    function randomPair(){
        return pairs[Math.floor(Math.random() * pairs.length)];
    }

    function hideModal(){
        if(dontShowAgain){
            localStorage.dontShow = true;
        }
        startModal.close();
    }

    function resetEverything(){
        localStorage.clear();
        location.reload();
    }

    function removePair(event){
        var removedPair = event.detail;
        pairs.push(removedPair);
        delete donePairs[removedPair];
        donePairCount--;
        savePairs()
    }

    function exportData(){
        var a = document.createElement("a");
        var text = "";

        Object.keys(donePairs).forEach(key => {
            text += `${key},${donePairs[key]}\n`;
        });

        console.log(text)

        var file = new Blob([text], {type: "text/plain"});
        a.href = URL.createObjectURL(file);
        a.download = "bldpairs.csv";

        a.click();
    }
</script>

<style>
    #title{
        margin-bottom:10vh;
    }

    #desc{
        padding:0rem 10rem;
    }

    button{
        border:1px solid black;
        border-radius: 0.3rem;

        background-color:#343434;
        color: var(--vanilla);

        padding:0.5rem 0.75rem;
        transition: 1s;
    }

    button:hover{
        border:1px solid var(--cornflower-blue);
        transition: 0.25s;
    }

    #modal{
        padding:4rem 2rem;
        max-width:50vw;
    }

    ::backdrop{
        background-color: rgba(0,0,0,0.5);
    }

    .pairs{
        /* display:flex;

        flex-direction: row;
        flex-wrap: wrap;
        flex-basis: calc(100% / 5);
        
        justify-content: center;

        gap:3rem; */
        display:grid;
        gap:3rem;
        grid-template-columns: 20fr 20fr 20fr 20fr 20fr;
    }
</style>