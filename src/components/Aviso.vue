<template>
    <main>
        <img src="../assets/images/alerta.png" alt="Aviso">
        <p>Todas as informações preenchidas neste site são sigilosas, não ficam salvas em nenhum banco de dados e não são compartilhadas com terceiros.</p>
        <button @click="$emit('iniciar')">Continuar</button>
    </main>
</template>

<script>
export default {
    name: 'Aviso',
}
</script>

<style scoped>
main {
    align-items: center;
}

img {
    width: 120px;
    margin-bottom: 30px;
}

button {
    background-color: #E90B0B;
    color: #FFF;
    font-size: 1.1em;
    width: 150px;
    padding: 12px;
    border-radius: 10px;
    margin-top: 40px;
}
</style>
