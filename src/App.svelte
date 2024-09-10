<script>
    // COMPONENTS
    import { onMount } from 'svelte';
    import Papa from 'papaparse';
    import * as turf from '@turf/helpers';
    import PointsWithinPolygon from '@turf/points-within-polygon';
    import RidingDetails from '$components/RidingDetails.svelte';
    import Select from "svelte-select"; // https://github.com/rob-balfre/svelte-select


    // DATA
    import { menuItems } from '$data/menu-items';
    import ridings from '$data/riding-boundaries-2024.js';
    const candidatesUrl = 'https://docs.google.com/spreadsheets/d/e/2PACX-1vTk-n-FsNcDDFKdo-zB665ebijtYBNE5G9i1WflJYgStgVItlvT26XmzBn_T1Vkn2lKkYggnkVAA2UJ/pub?gid=0&single=true&output=csv';
    const resultsUrl = 'https://docs.google.com/spreadsheets/d/e/2PACX-1vTk-n-FsNcDDFKdo-zB665ebijtYBNE5G9i1WflJYgStgVItlvT26XmzBn_T1Vkn2lKkYggnkVAA2UJ/pub?gid=715680360&single=true&output=csv';


    // VARIABLES
    let candidateData, resultsData, value;
    const defaultSelectValue = menuItems[0].value;
    let latlon = [-123.19361394392898, 49.400479859367884];

    // REACTIVE VARIABLES
    $: ridingName = '';
    $: ridingResults = [];
    $: ridingCandidates = [];
    $: value, updateData(value);

    async function fetchData(url) {
        return new Promise((resolve, reject) => {
            Papa.parse(url, {
                download: true,
                header: true,
                complete: results => {
                    resolve(results.data);
                },
                error: (err, file) => {
                    reject(err);
                }
            })
        });
        // const resp = await fetch(url);
        // data = await resp.text();
        // return csvParse(data);
    }

    function getCandidates(ridingName, data) {
        return data.filter(d => d.electoral_district === ridingName);
    }

    function getResults(ridingName, data) {
        return data.filter(d => d.electoral_district === ridingName);
    }

    function getRiding(latlon, riding) {
        let ridingName;
        const point = turf.point(latlon);
        // const point = turf.point([e.lngLat.lng, e.lngLat.lat]);

        // find out which riding the point is inside
	    ridings.features.forEach(d => {
		    // find which polygon the point is within
		    const withinPoly = PointsWithinPolygon(point, d);
		    // if so, let's cache the buffer
		    if (withinPoly.features.length > 0) {
                ridingName = d.properties.ED_NAME;
                return;
            }
	    });

        return ridingName;
    }

    function updateData(value) {
        if (!value || !value.value) return;

        // console.log(value);
    }

    async function init() {
        // fetch candidate data
        candidateData = await fetchData(candidatesUrl);
        console.log(candidateData);

        // fetch vote result data
        resultsData = await fetchData(resultsUrl);
        // console.log(resultsData);

        ridingName = getRiding(latlon, ridings);
        ridingResults = getResults(ridingName, resultsData);
        ridingCandidates = getCandidates(ridingName, candidateData);
        console.log(ridingResults)

        // default display selector value
		value = defaultSelectValue;
    }

    onMount(init);
</script>

<header>
    <h1>VS SvelteKit Template</h1>
    <p class="subhead">Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation</p>
</header>

<main>
    <Select items={menuItems}
        bind:value
        change={updateData}
        placeholder="Pick a city..."
		showChevron="true"
		listOpen={false}
    />

    <RidingDetails 
        riding={ridingName}
        results={ridingResults}
        candidates={ridingCandidates}
    />
    
</main>

<footer>
    <p class="note">NOTE: tk.</p>
    <p class="source">Source:  <a href="https:vancouversun.com" target="_blank">TK</a></p>
</footer>
  
<style>
    @import '$css/normalize.css';
    @import '$css/fonts.css';
    @import '$css/colors.css';
    @import '$css/app.css';

    header {
		margin-bottom: 2rem;
	}
	header > h1 {
		text-align: center;
	}
	header .subhead {
		margin: 0 auto;
		max-width: 525px;
		text-align: center;
	}

    /* COMBOBOX SELECTOR */
  	:global(.svelte-select) {
		margin: 1rem auto !important;
		max-width: 250px;
  	}
  	:global(input:focus) {
		outline: none;
  	}

	:global(
		.svelte-select .selected-item,
		.svelte-select .item,
		.svelte-select input
	) {
		font-family: 'BentonSansCond-Regular', sans;
	}
</style>
