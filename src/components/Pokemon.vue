<template>
    <div class="cuadro">
        <img src="https://via.placeholder.com/250" alt="No se puede cargar la imagen" />

        <label>{{ nombre }}</label>
    </div>
</template>
    
<script>
export default {
    props: {
        imagen: {
        },
        nombre: {
            type: String,
            default: "xxxx",
        },
    },
    data() {
        return {};
    },
};
</script>
    
<style scoped>
.cuadro {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    width: 200px;
    margin: 0 auto;
    background-color: rgb(255, 255, 255);
}

img {
    width: 200px;
    height: 300px;
    background-color: rgb(255, 255, 255);
}
</style>