<script>
    import { onMount } from 'svelte';
    import * as d3 from 'd3';

    onMount(async () => {
        d3.json('https://api.wiseoldman.net/v2/players/gloopt').then((d) => {
                console.log(d);
            });
    });
</script>

<h2>eindopdracht component</h2>

<svg width=1000>
    <g class='chart'></g>
</svg>