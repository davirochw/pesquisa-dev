<template>
    <div id="app">
        <HeaderComponent @changeValue="newSearchValue" @changeLinguagem="newLinguagemValue" @changeModo="newModoValue"></HeaderComponent>
        <GridComponent :devs="filtredDevs || devs"></GridComponent>
    </div>
</template>

<script>
import axios from 'axios'
import HeaderComponent from './components/HeaderComponent.vue'
import GridComponent from './components/GridComponent.vue'

export default {
    components: { HeaderComponent, GridComponent },
    name: 'App',
    data () {
        return {
            devs: [],
            search: null,
            modo: 'e',
            linguagem: [],
            filtredDevs: []
        }
    },
    created () {
        axios.get('https://bernardosantos.zeedhi.com/workfolder/dev.php').then((response) => {
            this.devs = response.data.devs
            this.filtredDevs = response.data.devs
            console.log(this.devs)
        })
    },
    methods: {
        newSearchValue: function (value) {
            this.search = value
            this.getFiltredDevs()
        },
        newModoValue: function (value) {
            this.modo = value
            this.getFiltredDevs()
        },
        newLinguagemValue: function (value) {
            this.linguagem = value
            this.getFiltredDevs()
        },
        getFiltredDevs: function () {
            let devs = this.devs
            let escopo = this

            escopo.filtredDevs = devs.filter( dev => {
                let semFiltro = !escopo.search && escopo.linguagem.length === 0 //se os dois estiverem vazios eu recebo TRUE
                let nomesBatendo = !escopo.search || escopo.getFilterCharacters(dev.name).indexOf(escopo.getFilterCharacters(escopo.search)) >= 0 //se um dos dois forem verdade eu recebo TRUE
                let linguagensBatendo = dev.programmingLanguages.filter(programming => escopo.linguagem.indexOf(escopo.getFilterCharacters(programming.id)) >= 0)

                if (escopo.modo === 'e') {    
                    if (semFiltro || (nomesBatendo && (linguagensBatendo.length === escopo.linguagem.length))) {
                        return true
                    }
                    return false;
                } else {
                    if (semFiltro || (nomesBatendo && (escopo.linguagem.length === 0 || linguagensBatendo.length >= 1))) {
                        return true;
                    }
                    return false;
                }
            })
        },
        getFilterCharacters: function (value) {
            return value.replaceAll(' ', '').toLowerCase()
        }
    }
}
</script>

<style>


</style>