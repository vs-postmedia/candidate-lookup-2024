<script>
    export let riding;
    export let results;
    export let candidates;
    export let riding2020;

    $: isVisible = candidates.length > 0 ? 'block' : 'none';
</script>

{#if candidates.length > 0}
    <div id="container" style="display:{isVisible}">
        <p class="riding-title">Candidates for</p>
        <h2 class="riding-name">{riding}</h2>
        {#if riding2020 !== riding}
            <p class="prev-riding">previously {riding2020}</p>
        {/if}

        <div id="candidates-list">
            <ul>
                {#each candidates as d}
                    <li style="border-left: 8px solid {d.party_color}">
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
                    <p class="name">Candidate</p>
                    <p class="party">Party</p>
                    <p style="flex-grow:2;">Vote %</p>
                    <p></p>
                </li>
                {#each results as d}
                    <li class="{d.elected}" style="border-left: 8px solid {d.party_color}">
                        <p class="name" style="background-color:{d.elected === 'Y' ? d.party_color : 'transparent'}; padding: 8px 4px;">{d.candidate}</p>
                        <p class="party">{d.party}</p>
                        <p class="vote-pct">{d.pct_votes}%</p>
                        <p class="bar">
                            <span class="color-bar" style="background:{d.party_color}; width:{d.pct_votes}%;">
                                <!-- <span style="background:{d.party_color}; width:{d.pct_votes}"></span> -->
                            </span>
                        </p>
                    </li>
                {/each}
            </ul>
        </div>
    </div>
{:else}
    <div id="no-data">
        <p>To find the candidates running in your riding this fall, enter a street name, postal code or location in the search box above and <bold>select an option from the menu that appears.</bold></p>
        <p>If you live in a densely populated area like Metro Vancouver, you may need to include a street number. For rural or remote areas, a street or town name is often enough.</p>
    </div>
{/if}


<style>
    #no-data {
        margin: 0 3vw;
        padding: 50px 0;
    }
    #no-data p {
        font-size: 1rem;
        padding: 5px 0;
    }
    #no-data p > bold {
        color: #0062A3;
        font-family: BentonSansCond-Bold;
    }
    #container {
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
    #container .prev-riding {
        color: var(--grey03);
        font-family: BentonSansCond-RegItalic;
        font-size: 0.8rem;
        margin: -20px 0 10px 0;
        text-align: center;
    }
    #candidates-list {
        margin-bottom: 50px;
    }
    #candidates-list a {
        color: var(--blue01);
        font-family: BentonSansCond-RegItalic, italic;
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
        flex-grow: 2
    }
    #results-list li,
    #candidates-list li {
        border-bottom: 1px solid var(--grey05);
        display: flex;
        /* padding: 8px 0; */
    }
    #results-list li > p,
    #candidates-list li > p {
        padding: 8px 4px;
    }
    #results-list .name,
    #candidates-list .name {
        flex-basis: 33%;
    }
    #results-list li.Y .name {
        color: #FFF;
        font-family: BentonSansCond-Bold, bold;
    }
    #results-list .party,
    #candidates-list .party {
        flex-grow: 3;
        padding: 8px 4px;
    }
    #results-list .vote-pct {
        padding: 8px 4px;
    }
    #results-list .bar {
        padding: 10px 4px;
        width: 50px;
    }
    #results-list .color-bar {
        display: block;
        height: 15px;
    }
</style>