<script>
    import { onMount } from 'svelte';
    import * as d3 from 'd3';

    onMount(async () => {
        let data = [30, 40, 10, 20, 60];

        const dataLinearScale = d3
            .scaleLinear()
            .domain([0, 99])
            .range([0, 400]);

        const dataColorLinearScale = d3
            .scaleLinear()
            .domain([0, 99])
            .range(['yellow', 'red']);

        let axis = d3.axisBottom(dataLinearScale);

        d3.select('#bottomAxis').call(axis);

        d3.select('#barChart')
            .selectAll('rect')
            .data(data)
            .join('rect')
            .attr('y', (d, i) => i * 60)
            .attr('height', 50)
            .attr('width', (d) => dataLinearScale(d))
            .attr('fill', (d) => dataColorLinearScale(d));
    });
</script>

<h1>Test</h1>
<p>Hello Hello</p>

<svg id="barChart" width="500" height="500">
    <g id="bottomAxis" transform="translate(0, 300)" />
</svg>
