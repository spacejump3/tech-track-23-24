<script>
    import { onMount } from 'svelte';
    import * as d3 from 'd3';

    onMount(async () => {

        // function getData() {
        //     fetch('https://api.wiseoldman.net/v2/players/gloopt')
        //         .then(res => res.json())
        //         .then(data => {
        //             console.log(data);
        //             return data;
        //         })  
        // };

        // const playerData = getData();

        // playerData.last.skills
        let data;

        async function getData() {
            const response = await fetch('https://api.wiseoldman.net/v2/players/gloopt');
            data = await response.json();
            return data
        };

        getData().then(data => {
            console.log(data.latestSnapshot.data.skills); // returns skills object
            console.log(Object.keys(data.latestSnapshot.data.skills)); // returns array of skills

            
        });

    });
</script>

<h2>eindopdracht component</h2>

<svg width=1000>
    <g class='chart'></g>
</svg>