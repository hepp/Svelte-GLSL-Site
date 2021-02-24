<!-- Simple hardcoded one-off component ATM -->

<script>

export let camRotate = { x: 0, y: 0 };

</script>

<style>
    div {
        position: relative;
        top: 50px;
        left: 70px;
        width: 80%;
        max-width: 2400px;
        pointer-events: none;
        background-color: rgb(27, 27, 27);
        opacity: .75;
        padding: .75em;
        padding-left: 1.5em;
    }

    h1 {
        font-size: 4em;
        -webkit-text-fill-color:white;
        margin: 0;
        padding-left: 20px;
    }

    h2 {
        -webkit-text-fill-color:rgb(119,162,179);
    }

    h3 {
        padding-left: 25px;
        -webkit-text-fill-color:lightgrey;
    }
</style>

<div style="transform: translateX({(((1-camRotate.x)+1) * 150) - 30}px);">
    <h2>Hi my name is:</h2>
    <h1>Hepp Maccoy</h1>
    <h3><i>I'm a Full-stack Developer that enjoys working on next-level projects</i></h3>
</div>