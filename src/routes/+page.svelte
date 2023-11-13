<script>
    import TestComponent from "../components/TestComponent.svelte";
    import Barchart from "../components/Barchart.svelte";
</script>

<body>
    <main>
        <Barchart />
        <TestComponent />
    </main>
</body>

<style>
    body {
        margin: 0;
        padding: 0;
        background: yellow;
    }

    main {
        margin: 0;
        padding: .1em;
        text-align: center;
        font-family: 'Courier New', Courier, monospace;
    }
</style>