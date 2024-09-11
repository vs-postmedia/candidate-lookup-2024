<script>
    export let riding;
    export let results;
    export let candidates;

    $: console.log(candidates)
</script>

<div id="container">
    <p class="riding-title">Riding:</p>
    <h2 class="riding-name">{riding}</h2>

    <div id="candidates-list">
        <ul>
            {#each candidates as d}
                <li>
                    <p class="name">{d.candidate}</p>
                    <p class="party">{d.party}</p>
                    <p>Link to <a href={d.url} target="_blank">bio</a></p>
                </li>
            {/each}
        </ul>
    </div>


</div>


<style>
    #container {
        border: 1px solid red;
        margin: 0 auto;
        max-width: 600px;
        padding: 2rem 0.25rem 1rem 0.25rem;
    }
    #container .riding-title {
        color: var(--grey02);
        font-family: BentonSansCond-RegItalic, italic;
        font-size: 1.1rem;
        margin-bottom: 7px;
        text-align: center;
        text-transform: uppercase;
    }
    #container .riding-name {
        font-size: 1.75rem;
        margin-bottom: 15px;
        text-align: center;
    }
    #candidates-list li {
        display: flex;
        margin-bottom: 5px;
    }
    #candidates-list li > p {
        flex-basis: 30%;
        padding: 0 5px;
    }
    #candidates-list p.name {
        font-family: BentonSansCond-Bold, bold;
    }
    #candidates-list p.party {
        flex-grow: 2;
    }
</style>