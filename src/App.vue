<template>
    <div id="app">
        <Demo/>
    </div>
</template>

<script>
    // import OpenSeadragon from "@/components/OpenSeadragon";
    import Demo from "@/components/Demo";
    export default {
        name: 'app',
        components: {
            Demo
        }
    }
</script>

<style>
    * {
        margin: 0;
        padding: 0;
    }

    html, body {
        width: 100%;
        height: 100%;
        overflow: hidden;
    }

    #app {
        height: 100%;
    }
</style>
