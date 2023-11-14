<script>
    import { onMount } from 'svelte';
    import * as d3 from 'd3';

    onMount(async () => {

        let data;
        let skillData;

        async function getData() {
            const response = await fetch('https://api.wiseoldman.net/v2/players/gloopt');
            data = await response.json();

            skillData = Object.values(data.latestSnapshot.data.skills); // Returns array of skills
            return data;
        };

        await getData(); // Wait for the getData() function to complete

        skillData.shift() // Remove the first datapoint

        console.log(skillData);

        // D3 barchart below
        const xScale = d3.scaleLinear()
            .domain([1, 99])
            .range([1, 500]);

        const yScale = d3.scaleBand()
            .domain(skillData.map(d => d.metric))
            .range([0, 800])
            .padding(0.1);

        const colorScale = d3.scaleLinear()
            .domain([0, 99])
            .range(['black', 'green'])

        d3.select('#skillBarChart').selectAll('rect')
            .data(skillData)
            .join('rect')

            .attr('width', d => xScale(d.level))
            .attr('height', yScale.bandwidth())
            .attr('y', d => yScale(d.metric))
        
            .attr('fill', d => colorScale(d.level));


    });
</script>

<h2>Eindopdracht component</h2>

<svg id="skillBarChart" width='500' height='800'></svg>