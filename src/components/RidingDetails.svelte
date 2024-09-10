<script>
    export let riding;
    export let results;
    export let candidates;

    $: console.log(candidates)
</script>

<div id="container">
    <h2 class="riding-name">{riding}</h2>
</div>