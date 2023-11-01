<script>
    import { onMount } from 'svelte';

    onMount(async () => {
        let randomNumber = Math.floor(Math.random() * 10);
        let randButton = document.getElementById('randomButton');

        randButton.addEventListener('click', () => {
            if (randomNumber === 0) {
                document.getElementById('box').style.background = "red";
            } else if (randomNumber === 1) {
                document.getElementById('box').style.background = "orange";
            } else if (randomNumber === 2) {
                document.getElementById('box').style.background = "yellow";
            } else if (randomNumber === 3) {
                document.getElementById('box').style.background = "green";
            } else if (randomNumber === 4) {
                document.getElementById('box').style.background = "blue";
            } else if (randomNumber === 5) {
                document.getElementById('box').style.background = "purple";
            } else if (randomNumber === 6) {
                document.getElementById('box').style.background = "magenta";
            } else if (randomNumber === 7) {
                document.getElementById('box').style.background = "pink";
            } else if (randomNumber === 8) {
                document.getElementById('box').style.background = "limegreen";
            } else if (randomNumber === 9) {
                document.getElementById('box').style.background = "hotpink";
            } else {
                document.getElementById('box').style.background = "black";
            }
            console.log(randomNumber);
        });
    });
</script>

<h1>Test</h1>
<p>LMAO is this actually working as intended????</p>
<button id="randomButton">PRESS ME LMAO</button>
<div id="box"></div>

<style>
    #box {
        margin-top: 1em;
        width: 20em;
        height: 20em;
        background-color: black;
    }

    button {
        color: white;
        background: green;
        width: 20em;
        height: 5em;
        box-shadow: .5em .5em 1em #0005;
        border: none;
    }

    button:hover {
        background: darkgreen;
        cursor: pointer;
    }

    button:active {
        background: black;
    }
</style>