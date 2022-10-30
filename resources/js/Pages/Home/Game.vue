<template>
    <div>
        <div class="h-10 bg-green-700 text-center  pt-2 pb-6">
            <p class="text-cool-gray-50 font-bold inline-block"> Hola {{usuario.name}}</p>
            <button class="inline-block ml-15 bg-gray-100 py-0 px-3 rounded text-green-700 font-bold" v-on:click="reload">Nuevo Juego</button>
        </div>
        <div>
            <iframe style="height: 94vh" class="w-full " :src="url" frameborder="0"></iframe>
        </div>
    </div>
</template>

<script>
export default {
    props: {
        usuario: Object,
    },
    data() {
        return {
            url: null,
        }
    },
    methods: {
        reload: function (event) {
            location.reload();
        }
    },
    beforeMount(){
        let usuario = localStorage.getItem('dni');
        if (usuario) {
            let url = "/BINGO/tablero_jugador.html";
            this.url = url;
        } else {
            let url = "/BINGO/index.html";
            this.url = url;
        }

    },
}
</script>