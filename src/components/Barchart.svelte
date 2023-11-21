<script>
    import * as d3 from 'd3';
    import { onMount } from 'svelte';
    
    let formData = { name: 'gloopt' };

    // Fetch API data
    async function getData(name) {
      const response = await fetch(
        `https://api.wiseoldman.net/v2/players/${name}`
      );
      return await response.json();
    };
  
    // Fetch data on button press
    async function handleSubmit() {  
      let data = await getData(formData.name);
      let skillData = Object.values(data.latestSnapshot.data.skills);

      skillData.shift(); // Remove the first datapoint

      console.log(skillData);

      generateChart(skillData);
    };

    // D3 charts
    function generateChart(skillData) {
        const attackLinks = [
            { source: 0, target: 1 },
            { source: 0, target: 2 },
            { source: 0, target: 3 },
            { source: 3, target: 4 },
            { source: 4, target: 5 },
            { source: 3, target: 6 },
            { source: 3, target: 7 },
            { source: 3, target: 8 },
            { source: 4, target: 9 },
            { source: 3, target: 10 },
            { source: 0, target: 11 },
            { source: 0, target: 12 }
        ];

        const nodes = skillData.map((skill, index, metric, rank) => ({ id: index, level: skill.level, metric: skill.metric, rank: skill.rank }));

        const links = attackLinks

        console.log(nodes);

        const simulation = d3.forceSimulation(nodes)
            .force('link', d3.forceLink(links).id(d => d.id).distance(100))
            .force("charge", d3.forceManyBody().strength(-30))
            .force("center", d3.forceCenter(300, 200));

        const svg = d3.select("svg");

        const link = svg.selectAll("line")
            .data(links)
            .enter().append("line")
            .attr('stroke', 'white');

        const node = svg.selectAll("circle")
            .data(nodes)
            .enter().append("circle")
            .attr('r', (d) => d.level * .3)
            .attr('fill', 'red')
            .call(d3.drag()
                .on("start", (event, d) => dragstarted(event, d))
                .on("drag", (event, d) => dragged(event, d))
                .on("end", (event, d) => dragended(event, d)));

        simulation.on("tick", function () {
            link.attr("x1", d => d.source.x)
                .attr("y1", d => d.source.y)
                .attr("x2", d => d.target.x)
                .attr("y2", d => d.target.y);

            node.attr("cx", d => d.x)
                .attr("cy", d => d.y);
        });

        function dragstarted(event, d) {
            if (!event.active) simulation.alphaTarget(0.3).restart();
            d.fx = d.x;
            d.fy = d.y;
        };

        function dragged(event, d) {
            d.fx = event.x;
            d.fy = event.y;
        };

        function dragended(event, d) {
            if (!event.active) simulation.alphaTarget(0);
            d.fx = null;
            d.fy = null;
        };

        simulation.restart();

        // const width = 500, height = 500;
        // const svg = d3.select('#bubbleChart')

        // const links = [
        //         {source: '0', target: '18'},
        //         {source: '1', target: '18'},
        //         {source: '2', target: '18'},
        //         {source: '3', target: '1'},
        //         {source: '4', target: '1'},
        //         {source: '5', target: '1'},
        //         {source: '6', target: '1'},
        //         {source: '7', target: '1'},
        //         {source: '8', target: '1'},
        //         {source: '9', target: '1'},
        //         {source: '10', target: '1'},
        //         {source: '11', target: '1'},
        //         {source: '12', target: '1'},
        //         {source: '13', target: '1'},
        //         {source: '14', target: '1'},
        //         {source: '15', target: '1'},
        //         {source: '16', target: '1'},
        //         {source: '17', target: '1'},
        //         {source: '18', target: '1'},
        //         {source: '19', target: '1'},
        //         {source: '20', target: '1'},
        //         {source: '21', target: '1'},
        //         {source: '22', target: '1'}
        //     ]
        
        // const node = svg
        //     .append('g')
        //     .selectAll('circle')
        //     .data(skillData)
        //     .enter()
        //     .append('circle')

        // const link = svg
        //     .append('g')
        //     .selectAll('line')
        //     .data(chart.links)
        //     .enter()
        //     .append('line')
        //     .attr('stroke-width', function(d) {
        //         return 3;
        //     })
        //     .style('stroke', 'pink')

        // const simulation = d3.forceSimulation(skillData)
        //     .force('charge', d3.forceManyBody().strength(-10))
        //     .force('center', d3.forceCenter(width / 2, height / 2))
        //     .on('tick', ticked);

        // function ticked() {
        //     node
        //         .join('circle')
        //         .attr('r', function(d) { 
        //             return .3 * d.level; 
        //         })
        //         .attr('cx', function(d) {
        //             return d.x
        //         })
        //         .attr('cy', function(d) {
        //             return d.y
        //         })
        //         .attr('fill', '#0005')
        //         .attr('stroke', 'grey')
        // };
    };
  </script>

<div class='formContainer'>
    <form on:submit={handleSubmit}>
        <label for='name'>Enter username</label>
        <input type='text' placeholder='First username...' id='name' bind:value={formData.name} />
        <input type='text' placeholder='Second username...' />
        <button type='submit'>Compare</button>
    </form>
</div>

<svg id='bubbleChart' width='1000' height='1000'></svg>

<style>
    form {
        margin: 1em;
        padding: 1em;

        display: flex;
        flex-direction: column;
    }

    form label {
        color: yellow;
        font-weight: 600;
    }

    form input {
        height: 3em;
        margin: 1em 0 0 0;
        padding: 0 1em 0 1em;

        background: #1E0F0F;
        color: white;
        border: none;
        border-radius: .1em;
    }

    form input:focus {
        outline: solid #554844 1px;
    }

    form button {
        height: 3em;
        margin: 1em 0 0 0;
        color: white;
        font-weight: 600;

        background: #3A961A;
        border: none;
        border-radius: .1em;
    }

    form button:hover {
        background: #53bf30;
        cursor: pointer;
    }
</style>