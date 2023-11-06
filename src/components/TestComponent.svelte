<script>
    import { onMount } from 'svelte';
    import * as d3 from 'd3';

    onMount(async () => {
        const getPlayerInfo = async () => {
        const res = await fetch('https://api.wiseoldman.net/v2/players/gloopt');
        const data = await res.json();
        console.log(data);
    }

    getPlayerInfo(); 

    let data = [30, 40, 10, 20, 60];

    d3.select('#barChart')
        .selectAll('rect')
        .data(data)
        .join('rect')
        .attr('x', (d,i) => (i*60))
        .attr('width', 50)
        .attr('y', (d) => (300 - d*5))
        .attr('height', (d) => (d*5))
        .text('h');
    })
</script>

<h1>Test</h1>
<p>Hello Hello</p>

<svg id='barChart' width=500 height=500></svg>