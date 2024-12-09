<template>
    <main>
        <img src="../assets/images/alerta.png" alt="Aviso">
        <p>Os resultados deste teste são baseados nos critérios do Hemope. Caso você vá doar em outro hemocentro, pode
            ser que existam discrepâncias.</p>
        <p>Todas as informações preenchidas neste site são sigilosas, não ficam salvas em nenhum banco de dados e não
            são compartilhadas com terceiros.</p>
        <p>Responda todas as perguntas, ou o teste poderá dar um resultado incorreto.</p>
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
    background-color: var(--vermelho);
    color: var(--branco);
    font-size: 1.1em;
    width: 150px;
    padding: 12px;
    border-radius: 10px;
    margin-top: 20px;
}

p {
    margin-bottom: 20px;
}
</style>
