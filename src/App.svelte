<script>
    // COMPONENTS
    import Papa from 'papaparse';
    import { onMount } from 'svelte';
    // import '$lib/autocomplete-element';
    import * as turf from '@turf/helpers';
    import { Geocoder, controls } from '@beyonk/svelte-mapbox';
    import PointsWithinPolygon from '@turf/points-within-polygon';
    import RidingDetails from '$components/RidingDetails.svelte';
    // import Autocomplete from '$components/Autocomplete.svelte';
    // import GeocodingControl from "@maptiler/geocoding-control/svelte/GeocodingControl.svelte";

    // DATA
    import ridings2024 from '$data/riding-boundaries-2024.js';
    import ridings2020 from '$data/riding-boundaries-2020.js';
    const mapboxToken = import.meta.env.VITE_MAPBOX_TOKEN;
    // const mapTilerApiKey = import.meta.env.VITE_MAPTILER_API_KEY;
    const geApiKey = import.meta.env.VITE_GE_API_KEY;
    const candidatesUrl = 'https://docs.google.com/spreadsheets/d/e/2PACX-1vTk-n-FsNcDDFKdo-zB665ebijtYBNE5G9i1WflJYgStgVItlvT26XmzBn_T1Vkn2lKkYggnkVAA2UJ/pub?gid=0&single=true&output=csv';
    const resultsUrl = 'https://docs.google.com/spreadsheets/d/e/2PACX-1vTk-n-FsNcDDFKdo-zB665ebijtYBNE5G9i1WflJYgStgVItlvT26XmzBn_T1Vkn2lKkYggnkVAA2UJ/pub?gid=715680360&single=true&output=csv';
    const geScriptUrl = 'https://cdn.jsdelivr.net/npm/@geocodeearth/autocomplete-element/dist/bundle.js';
    const testUrl = `https://api.geocode.earth/v1/autocomplete?api_key=${geApiKey}&text=vancouver`


    // VARIABLES
    let candidateData, resultsData;
    const bcBbox = [-139.595032,48.302552,-113.887024,60.199227];
    const { GeolocateControl } = controls;

    // REACTIVE VARIABLES
    $: ridingResults = [];
    $: riding2020 = '';
    $: riding2024 = '';
    $: ridingCandidates = [];

    function test(url) {
        fetch(url)
            .then(response => {
                if (!response.ok) {
                    throw new Error('Network response was not ok');
                }
                return response.json();
            })
            .then(data => {
                console.log(data);
                console.log(data.features)
            })
            .catch(error => {
                console.error('Error fetching autocomplete results:', error);
            });

    }


    // async function addGeocodeEarthScript(url) {
    //     const script = document.createElement('script');
    //     script.src = url;
    //     script.type = 'module';
    //     document.body.appendChild(script)
    // }
    async function addGeocodeEarthScript(url) {
        return new Promise((resolve, reject) => {
            const script = document.createElement('script');
            script.src = url;
            script.type = 'module';
            script.onload = resolve;
            script.onerror = reject;
            document.body.appendChild(script)
        })
    }

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
    }

    function getCandidates(ridingName, data) {
        return data.filter(d => d.electoral_district === ridingName)
            .sort((a,b) => a.candidate.split(' ')[1].localeCompare(b.candidate.split(' ')[1]));
    }

    function getRidingResults(ridingName, data) {
        return data.filter(d => d.ed_name === ridingName)
            .sort((a,b) => parseFloat(b.pct_votes) - parseFloat(a.pct_votes));
    }

    function getRiding(latlon, ridings) {
        let ridingName;
        const point = turf.point(latlon);

        // find out which riding the point is inside
	    ridings.features.forEach(d => {
		    // find which polygon the point is within
		    const withinPoly = PointsWithinPolygon(point, d);
		    if (withinPoly.features.length > 0) {
                ridingName = d.properties.ED_NAME;
                return;
            }
	    });

        return ridingName;
    }

    function handleGeocodeError(e) {
        console.log(e);
    }

    function handleGeocodeResults(e) {
        console.log(e.detail)
        if (e.detail !== null) {
            const latlon = e.detail.result.center;
            // const latlon = e.detail.center; // maptiler
            // const latlon = e.detail.geometry.coordinates; // geocode earth
            riding2024 = getRiding(latlon, ridings2024); // ridings2024
            riding2020 = getRiding(latlon, ridings2020); // ridings2020

            ridingResults = getRidingResults(riding2020, resultsData);
            // candidates always uses 2024 riding info
            ridingCandidates = getCandidates(riding2024, candidateData);
        }
    }

    function handleGeocodeEarthEvents() {
        const el = document.querySelector('ge-autocomplete');

        el.addEventListener('select', handleGeocodeResults);

        el.addEventListener('error', e => {
            console.log(e.detail, e)
        });
    }

    async function init() {


        // test(testUrl)
        // await addGeocodeEarthScript(geScriptUrl);


        // fetch candidate data
        candidateData = await fetchData(candidatesUrl);

        // fetch vote result data
        resultsData = await fetchData(resultsUrl);

        // event handling for GeocodeEarth autocomplete element
        // handleGeocodeEarthEvents();
    }

    onMount(init);

    function filterResults(e) {
        console.log(e)
        return !e.place_name_en.includes('Alberta');
    }

    console.log()
</script>

<header>
    <h1>Who’s running in my riding?</h1>
</header>

<main>
    
    <div class="geocoding">
        <span class="voting-emoji">🗳️</span>

        <Geocoder 
            accessToken={mapboxToken}
            on:result={handleGeocodeResults}
            on:error={handleGeocodeError}
        />

        <!-- <ge-autocomplete
            api_key={geApiKey}
            boundary.country='CA'
            boundary.gid='whosonfirst:region:85682117'
            placeholder='Enter an address or city'
        ></ge-autocomplete> -->

        <!-- <GeocodingControl 
            apiKey={mapTilerApiKey}
            bbox={bcBbox}
            country='ca'
            filter={filterResults}
            limit=10
            minLength=2
            placeholder='Lookup an address or location...'
            proximity={[
                { type: "client-geolocation" },
                { type: "server-geolocation" }
            ]}
            on:pick={handleGeocodeResults}
            on:error={handleGeoCodeError}
        /> -->
        <span class="voting-emoji">🗳️</span>
    </div>

    <RidingDetails 
        riding={riding2024}
        riding2020={riding2020}
        results={ridingResults}
        candidates={ridingCandidates}
    />
    
</main>

<footer>
    <p class="note">NOTE: Parties have until Sept. 28 to register candidates for the election. They are not required to run candidates in every riding.</p>
    <p class="source">Source:  <a href="https://elections.bc.ca/2024-provincial-election/candidate-list/" target="_blank">Elections B.C.</a>, Political parties</p>
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
	/* header .subhead {
		margin: 0 auto;
		max-width: 525px;
		text-align: center;
	} */
    .geocoding {
        display: flex;
        justify-content: center;
        margin: 0 auto;
    }
    .geocoding .voting-emoji {
        font-size: 2rem;
        padding: 4px 8px 0 8px;
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
