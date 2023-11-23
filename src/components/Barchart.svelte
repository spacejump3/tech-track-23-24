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

      // add imagePath to each node
      skillData = skillData.map((d, i) => ({
        ...d,
        imagePath: `/static/img/${i}.png`
      }))

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
            { source: 0, target: 12 },
            { source: 0, target: 13 },
            { source: 0, target: 14 },
            { source: 0, target: 15 },
            { source: 0, target: 16 },
            { source: 0, target: 17 },
            { source: 0, target: 18 },
            { source: 0, target: 19 },
            { source: 0, target: 20 },
            { source: 0, target: 21 },
            { source: 0, target: 22 },
        ];

        const nodes = skillData.map((d, i) => ({ 
            id: i, 
            level: d.level, 
            metric: d.metric, 
            rank: d.rank, 
            experience: d.experience,
            imagePath: d.imagePath 
        }));

        const links = attackLinks

        console.log(nodes);

        const simulation = d3.forceSimulation(nodes)
            .force('link', d3.forceLink(links).id(d => d.id).distance(100))
            .force("charge", d3.forceManyBody().strength(-70))
            .force("center", d3.forceCenter(300, 200));

        const svg = d3.select("svg");

        svg.selectAll('*').remove();

        const link = svg.selectAll("line")
            .data(links)
            .enter().append("line")
            .attr('stroke', '#FFF2');

        const node = svg.selectAll("g")
            .data(nodes)
            .enter().append("g")
            .call(d3.drag()
                .on("start", (event, d) => dragstarted(event, d))
                .on("drag", (event, d) => dragged(event, d))
                .on("end", (event, d) => dragended(event, d))
            )

        node.append("circle")
            .attr('r', (d) => d.experience * .000003)
            .attr('fill', 'red')
            .on("mouseover", function (event, d) {
                d3.select(this.parentNode).select('text')
                    .style("fill", "white")
                    .style('text-shadow', '.1em .1em .1em #000')
            })
            .on("mouseout", function (event, d) {
                d3.select(this.parentNode).select('text')
                    .style("fill", "#FFF1")
                    .style('text-shadow', 'none')
            });;

        node.append("image")
            .attr('xlink:href', d => d.imagePath)
            .attr('x', d => -0.5 * d.experience * 0.000003) // Adjust the x-coordinate based on the circle's radius
            .attr('y', d => -0.5 * d.experience * 0.000003) // Adjust the y-coordinate based on the circle's radius
            .attr('width', d => d.experience * 0.000006) // Set the width based on the circle's radius
            .attr('height', d => d.experience * 0.000006); // Set the height based on the circle's radius

        node.append('text')
            .text(d => d.metric)
            .attr('dy', d => d.experience * 0.000003 + 15)
            .attr('text-anchor', 'middle')
            .attr('fill', '#FFF1')

        simulation.on("tick", function () {
            link.attr("x1", d => d.source.x)
                .attr("y1", d => d.source.y)
                .attr("x2", d => d.target.x)
                .attr("y2", d => d.target.y);

            node.attr("cx", d => d.x)
                .attr("cy", d => d.y);

            node.attr("transform", d => `translate(${d.x},${d.y})`);
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