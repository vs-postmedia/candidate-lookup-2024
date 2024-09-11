<script>
    export let riding;
    export let results;
    export let candidates;

    $: console.log(candidates)
    $: isVisible = candidates.length > 0 ? 'block' : 'none';
</script>

<div id="container" style="display:{isVisible}">
    <p class="riding-title">Candidates for</p>
    <h2 class="riding-name">{riding}</h2>

    <div id="candidates-list">
        <ul>
            {#each candidates as d}
                <li>
                    <p class="name">{d.candidate}</p>
                    <p class="party">{d.party}</p>
                    <p><a href={d.url} target="_blank">View bio</a></p>
                </li>
            {/each}
        </ul>
    </div>

    <div id="results-list" style="display:{isVisible}">
        <h3>2020 election results</h3>
        <ul>
            <li class="header">
                <p>Candidate</p>
                <p class="party">Party</p>
                <p>Vote %</p>
            </li>
            {#each results as d}
                <li>
                    <p class="name">{d.candidate}</p>
                    <p class="party">{d.party}</p>
                    <p>{d.pct_votes}%</p>
                </li>
            {/each}
        </ul>
    </div>
</div>


<style>
    #container {
        /* border: 1px solid red; */
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
    }
    #container .riding-name {
        font-size: 1.75rem;
        margin-bottom: 20px;
        text-align: center;
    }
    #candidates-list {
        margin-bottom: 50px;
    }
    #results-list h3 {
        color: var(--grey01);
        font-family: BentonSansCond-BoldItalic;
        font-size: 1.25rem;
        margin-bottom: 15px;
        text-align: center;
    }
    #results-list .header {
        color: var(--grey05);
        font-size: 0.8rem;
    }
    #results-list .header p.party {
        flex-grow: 1
    }
    #results-list li,
    #candidates-list li {
        border-bottom: 1px solid var(--grey05);
        display: flex;
        margin: 10px 0;
        padding-bottom: 3px;
    }
    #results-list li > p,
    #candidates-list li > p {
        padding: 0 5px;
    }
    #results-list .name,
    #candidates-list .name {
        flex-basis: 33%;
        /* font-family: BentonSansCond-Bold, bold; */
    }
    #results-list .party,
    #candidates-list .party {
        flex-grow: 3;
    }
</style>