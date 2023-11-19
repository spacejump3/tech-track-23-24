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

    // D3 barchart
    function generateChart(skillData) {
        const width = 800, height = 300;
        const margin = { top: 30, right: 30, bottom: 30, left: 30 };

        const svg = d3.select('svg')
        .attr('width', width)
        .attr('height', height);

        svg.selectAll('*').remove(); // remove the previous chart before creating a new chart

        const xScale = d3.scaleBand()
        .domain(['attack', 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22])
        .range([0, width - (margin.left + margin.right)]);

        const yScale = d3.scaleLinear()
        .domain([1, 99])
        .range([height - (margin.top + margin.bottom) * 2, 0]);

        svg.append('g')
        .attr('transform', 'translate('+ margin.top + ',' + margin.left +')')
        .selectAll('rect')
        .data(skillData)
        .enter()
        .append('rect')
            .attr('x', function(d, i) {
            return xScale(i);
            })
            .attr('y', function(d, i) {
            return yScale(d.level);
            })
            .attr('width', xScale.bandwidth())
            .attr('height', function(d, i) { 
            return height - (margin.top + margin.bottom) - yScale(d.level); 
            })
            .attr('fill', 'brown')
            .style('stroke', 'black')
            .style('stroke-width', 1);

        svg.append('g')
        .attr('transform', 'translate('+ margin.left +','+ (height - margin.top) +')')
        .call(d3.axisBottom(xScale)); 

        svg.append('g')
        .attr('transform', 'translate('+ margin.left +','+ margin.top +')')
        .call(d3.axisLeft(yScale));
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

<svg></svg>

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