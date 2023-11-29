<script>
    import * as d3 from 'd3';

    let formData = { name: '' };

    // Fetch API data
    async function getData(name) {
        const response = await fetch(
            `https://api.wiseoldman.net/v2/players/${name}`
        );
        return await response.json();
    }

    // Fetch data on button press
    async function handleSubmit() {
        let data = await getData(formData.name);
        let skillData = Object.values(data.latestSnapshot.data.skills);

        skillData.shift(); // Remove the first datapoint

        // add imagePath to each node
        skillData = skillData.map((d, i) => ({
            ...d,
            imagePath: `/img/${i}.png`,
        }));

        console.log(skillData);

        generateChart(skillData);
    }

    // D3 charts
    function generateChart(skillData) {
        const links = [
            { source: 18, target: 20 },
            { source: 18, target: 1 },
            { source: 18, target: 5 },
            { source: 18, target: 10 },
            { source: 18, target: 19 },
            { source: 18, target: 13 },
            { source: 14, target: 13 },

            { source: 16, target: 20 },
            { source: 20, target: 6 },

            { source: 6, target: 1 },
            { source: 6, target: 18 },

            { source: 0, target: 18 },
            { source: 0, target: 1 },
            { source: 0, target: 2 },
            { source: 0, target: 3 },

            { source: 10, target: 7 },
            { source: 11, target: 7 },
            { source: 8, target: 11 },
            { source: 8, target: 9 },
            { source: 8, target: 22 },

            { source: 9, target: 4 },
            { source: 4, target: 18 },
            { source: 12, target: 4 },
            { source: 21, target: 12 },
            { source: 19, target: 21 },
            { source: 17, target: 19 },
            { source: 19, target: 15 },
            { source: 19, target: 21 },
        ];

        const nodes = skillData.map((d, i) => ({
            id: i,
            level: d.level,
            metric: d.metric,
            rank: d.rank,
            experience: d.experience,
            imagePath: d.imagePath,
        }));

        console.log(nodes);

        const simulation = d3
            .forceSimulation(nodes)
            .force(
                'link',
                d3
                    .forceLink(links)
                    .id((d) => d.id)
                    .distance(150)
            )
            .force('charge', d3.forceManyBody().strength(-170))
            .force('center', d3.forceCenter(500, 500));

        const svg = d3.select('svg');

        svg.selectAll('*').remove();

        const link = svg
            .selectAll('line')
            .data(links)
            .enter()
            .append('line')
            .attr('stroke', '#FFF2')
            .attr('stroke-width', 2)
            .attr('marker-end', 'url(#end)');

        svg.append('defs')
            .selectAll('marker')
            .data(['end'])
            .enter()
            .append('marker')
            .attr('id', (d) => d)
            .attr('viewBox', '0 -5 10 10')
            .attr('refX', 50) // Controls the distance from the end of the line to the arrowhead
            .attr('refY', 0) // Controls the vertical offset
            .attr('markerWidth', 6)
            .attr('markerHeight', 6)
            .attr('orient', 'auto')
            .attr('fill', 'grey')
            .append('path')
            .attr('d', 'M0,-5L10,0L0,5');

        const node = svg
            .selectAll('g')
            .data(nodes)
            .enter()
            .append('g')
            .call(
                d3
                    .drag()
                    .on('start', (event, d) => dragstarted(event, d))
                    .on('drag', (event, d) => dragged(event, d))
                    .on('end', (event, d) => dragended(event, d))
            );

        const sqrtScale = d3
            .scaleSqrt()
            .domain([0, d3.max(nodes, (d) => d.experience)])
            .range([5, 100]);

        const rankCol = d3
            .scaleLinear()
            .domain([0, d3.max(nodes, (d) => d.rank)])
            .range(['#7fde50', '#802c22']);

        node.append('circle')
            .attr('r', (d) => sqrtScale(d.experience))
            .attr('fill', (d) => rankCol(d.rank))
            .attr('visibility', 'visible')
            .on('mouseover', function () {
                d3.select(this.parentNode)
                    .selectAll('text')
                    .attr('visibility', 'visible')
                    .style('fill', 'white')
                    .style('text-shadow', '.1em .1em .1em #000');

                d3.select(this)
                    .style('stroke', 'white')
                    .style('stroke-width', 2);
            })
            .on('mouseout', function () {
                d3.select(this.parentNode)
                    .selectAll('text')
                    .attr('visibility', 'hidden')
                    .style('fill', '#FFF1')
                    .style('text-shadow', 'none');

                d3.select(this).style('stroke', 'none');
            });

        node.append('image')
            .attr('xlink:href', (d) => d.imagePath)
            .attr('x', (d) => -sqrtScale(d.experience) / 2)
            .attr('y', (d) => -sqrtScale(d.experience) / 2)
            .attr('width', (d) => sqrtScale(d.experience))
            .attr('height', (d) => sqrtScale(d.experience))
            .style('image-rendering', 'crisp-edges')
            .style('filter', function (d) {
                if (d.level >= 99) {
                    return 'drop-shadow(0px 0px 1px yellow) drop-shadow(0px 0px 1px yellow) drop-shadow(0px 0px 1px yellow) drop-shadow(0px 0px 1px yellow)';
                } else {
                    return 'none';
                }
            })
            .on('mouseover', function () {
                d3.select(this.parentNode)
                    .selectAll('text')
                    .attr('visibility', 'visible')
                    .style('fill', 'white')
                    .style('text-shadow', '.1em .1em .1em #000');

                d3.select(this.parentNode)
                    .select('circle')
                    .style('stroke', 'white')
                    .style('stroke-width', 2);
            })
            .on('mouseout', function () {
                d3.select(this.parentNode)
                    .selectAll('text')
                    .attr('visibility', 'hidden')
                    .style('fill', '#FFF1')
                    .style('text-shadow', 'none');

                d3.select(this.parentNode)
                    .select('circle')
                    .style('stroke', 'none');
            });

        node.append('text')
            .text((d) => `Skill: ${d.metric}`)
            .attr('dy', (d) => sqrtScale(d.experience) + 15)
            .attr('text-anchor', 'middle')
            .attr('fill', '#FFF1')
            .attr('visibility', 'hidden');

        node.append('text')
            .text((d) => `Level: ${d.level}`)
            .attr('dy', (d) => sqrtScale(d.experience) + 30)
            .attr('text-anchor', 'middle')
            .attr('fill', '#FFF1')
            .attr('visibility', 'hidden');

        node.append('text')
            .text((d) => `Rank: ${d.rank}`)
            .attr('dy', (d) => sqrtScale(d.experience) + 45)
            .attr('text-anchor', 'middle')
            .attr('fill', '#FFF1')
            .attr('visibility', 'hidden');

        simulation.on('tick', function () {
            link.attr('x1', (d) => d.source.x)
                .attr('y1', (d) => d.source.y)
                .attr('x2', (d) => d.target.x)
                .attr('y2', (d) => d.target.y);

            node.attr('cx', (d) => d.x).attr('cy', (d) => d.y);

            node.attr('transform', (d) => `translate(${d.x},${d.y})`);
        });

        function dragstarted(event, d) {
            if (!event.active) simulation.alphaTarget(0.3).restart();
            d.fx = d.x;
            d.fy = d.y;
        }

        function dragged(event, d) {
            d.fx = event.x;
            d.fy = event.y;
        }

        function dragended(event, d) {
            if (!event.active) simulation.alphaTarget(0);
            d.fx = null;
            d.fy = null;
        }

        simulation.restart();
    }
</script>

<div class="formContainer">
    <form on:submit={handleSubmit}>
        <label for="name">Enter username</label>
        <input
            type="text"
            placeholder="Username..."
            id="name"
            bind:value={formData.name}
        />

        <select bind:value={formData.name} for="name" name="names" id="">
            <option value="">Or select one of these names</option>
            <option value="gloopt">gloopt</option>
            <option value="fatpear 7">fatpear 7</option>
            <option value="lynx titan">lynx titan</option>
            <option value="b0aty">b0aty</option>
        </select>
        <button type="submit">Submit</button>
    </form>
</div>

<svg id="bubbleChart" width="1500" height="1200" />

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

        background: #1e0f0f;
        color: white;
        border: none;
        border-radius: 0.1em;
    }

    form input::placeholder {
        opacity: 1;
        color: #FFF5;
    }

    form input:focus {
        outline: solid #554844 1px;
    }

    form select {
        height: 3em;
        margin: 1em 0 0 0;
        padding: 0 1em 0 1em;

        background: #1e0f0f;
        color: #FFF5;
        border: none;
        border-radius: 0.1em;

        cursor: pointer;
    }

    form select:hover {
        color: white;
    }

    form button {
        height: 3em;
        margin: 1em 0 0 0;
        color: white;
        font-weight: 600;

        background: #3a961a;
        border: none;
        border-radius: 0.1em;
    }

    form button:hover {
        background: #7fde50;
        cursor: pointer;
    }
</style>
