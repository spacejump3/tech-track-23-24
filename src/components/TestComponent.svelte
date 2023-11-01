<script>
    import { onMount } from 'svelte';

     const getPlayerInfo = async () => {
        const res = await fetch('https://api.wiseoldman.net/v2/players/gloopt');
        const data = await res.json();
        console.log(data);
    }

    getPlayerInfo(); 

</script>

<h1>Test</h1>
<p>Hello Hello</p>