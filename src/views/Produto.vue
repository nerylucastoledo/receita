<template>

    <section class="container produto">

        <div v-if="!carregou">
            <PageLoading/>
        </div>

        <div v-else>

            <div v-if="pessoa" class="pessoa">

                <div v-if="pessoa.foto">
                    <img class="foto-pessoa" :src="pessoa.foto" alt="Foto de Perfil">
                </div>

                <div class="info-pessoa">
                    <p>Feito por <span>{{pessoa.nome}}</span></p>
                    <p>de <span>{{pessoa.cidade}}</span></p>
                </div>

            </div>

            <div v-if="produto">

                <div class="introducao-produto">
                    <div>
                        <img :src="produto.imagem" alt="Imagem do produto">
                    </div>

                    <div>
                        <h1>{{produto.nome_receita}}</h1>
                        <p>{{produto.descricao}}</p>
                    </div>
                </div>

                <div class="infos-produto">
                    
                    <div class="items">
                        <font-awesome-icon icon="users"/>
                        <h2>Pessoas</h2>
                        <p>{{produto.serve}}</p>
                    </div>

                    <div class="items">
                        <font-awesome-icon icon="clock"/>
                        <h2>Tempo</h2>
                        <p>{{produto.tempo}} min</p>
                    </div>

                    <div class="items">
                        <font-awesome-icon icon="utensils"/>
                        <h2>Categoria</h2>
                        <p>{{produto.categoria}}</p>
                    </div>

                    <div class="items">
                        <font-awesome-icon icon="tools"/>
                        <h2>Dificuldade</h2>
                        <p>{{produto.dificuldade}}</p>
                    </div>

                </div>

                <div class="ingredientes items">

                    <h2>Ingredientes</h2>

                    <div v-for="(ingredientes, index) in produto.ingredientes" :key="ingredientes+index">
                        <input type="checkbox">
                        <label for="">{{ingredientes}}</label>
                    </div>

                </div>

                <div class="modo-preparo items">

                    <h2>Modo de preparo</h2>

                    <div v-for="(passo, index) in produto.modo_preparo" :key="passo+index">
                        <p>{{index + 1}} - {{passo}}</p>
                    </div>

                </div>

            </div>

            <div class="disqus">
                <Disqus ref="disqus"/>
            </div>
        </div>

    </section>
</template>

<script>
import { api } from '../service.js'

import PageLoading from "../components/PageLoading.vue"

export default {
    name: "Produto",

    components: {
        PageLoading,
    },

    props: ["id"],

    data() {
        return {
            produto: null,
            pessoa: null,
            carregou: false,
        }
    },

    methods: {
        mostrarProdutoSelecionado() {
            api.get(`receita/?id=${this.id}`)
            .then(res => {
                const ingredientesFormatado = res.data.results[0].ingredientes.split(",")
                const modoPreparoFormatado = res.data.results[0].modo_preparo.split(";")
                const emailDoCriador = res.data.results[0].email_criador

                this.produto = res.data.results[0]
                this.produto.ingredientes = ingredientesFormatado
                this.produto.modo_preparo = modoPreparoFormatado
                
                this.mostrarPessoaQueCriou(emailDoCriador)
                setTimeout(() => {
                    this.carregou = true
                }, 1000);

                document.title = `Receita - ${res.data.results[0].nome_receita}`
            })
        },

        mostrarPessoaQueCriou(email) {
            api.get(`usuario/?email=${email}`)
            .then(res => {
                this.pessoa = res.data[0]
            })
        }

    },

    created() {
        this.mostrarProdutoSelecionado()
    }
    
}
</script>

<style scoped>

.produto {
    margin-top: 60px;
    font-size: 1rem;
    font-weight: 500;
}

.pessoa {
    display: flex;
    align-items: center;
}

.foto-pessoa {
    width: 80px;
    height: 80px;
    border-radius: 50%;
    object-fit: cover;
}

.info-pessoa {
    margin-left: 10px;
    margin-bottom: 10px;
}

.info-pessoa span {
    color: #759F41;
}

.introducao-produto {
    display: flex;
    flex: 1;
}

.introducao-produto img {
    width: 600px;
    max-height: 400px;
    object-fit: cover;
    border-radius: 10px;
}

.introducao-produto p, .introducao-produto h1 {
    margin-left: 20px;
}

.introducao-produto p {
    margin-top: 80px;
}

.infos-produto {
    display: flex;
    margin-top: 40px;
    justify-content: space-around;
    text-align: center;
    max-width: 100%;
    flex-wrap: wrap;
}

.items {
    background-color: #F3F5F6;
    padding: 20px 30px;
    margin: 10px 0px;
    border-radius: 10px;
}

.items h2 {
    font-size: 24px;
    color: #759F41;
    text-align: center;
}

.items p {
    color: #000;
    margin-top: 10px;
}

.ingredientes {
    margin-top: 40px;
    margin-bottom: 60px;
}


.ingredientes h2 {
    margin-bottom: 10px;
}

.ingredientes > div {
    margin: 20px 0px;
    box-shadow: 0 4px 8px rgb(30 60 90 / 10%);
    padding: 10px;
    font-size: 18px;
}

input {
    width: 20px;
    padding: 0px;
    margin: 0px 10px 0px;
}

::marker {
    color: #759F41;
}

.modo-preparo p {
    margin-top: 20px;
}

.infos-produto h2 {
    font-size: 18px;
}

@media (max-width: 750px) {
    .introducao-produto {
        display: block;
    }
    .introducao-produto p {
        margin-top: 20px;
    }
    .introducao-produto img {
        max-width: 100%;
        max-height: 100%;
    }
    .introducao-produto p, .introducao-produto h1 {
        margin-left: 0px;
        text-align: center;
    }
    .items {
        width: 100%;
    }
}

.disqus {
    margin-top: 60px;
}
</style>