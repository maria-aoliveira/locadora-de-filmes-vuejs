<template>
  <div id="app">
    <div>
      <b-navbar type="dark" variant="dark">
        <b-navbar-nav>
          <b-nav-item @click="mostrar = true" href="#">Home</b-nav-item>
          <b-nav-item @click="mostrar = false" href="#">Carrinho</b-nav-item>
        </b-navbar-nav>
      </b-navbar>
    </div>

    <b-container>
      <b-row>
        <h1>{{ title }}</h1>
      </b-row>
      <b-row>
        <HelloWorld msg="hello!" />
      </b-row>
      <b-row>
        <p>{{ parteDoDia() }}</p>
      </b-row>
      <b-row v-show="mostrar">
        <p class="m-2">Carrinho: {{ qtCarrinho }}</p>
      </b-row>
      <b-row v-show="mostrar">
        <div class="col md-3" v-bind:key="filme.id" v-for="filme in filmes">
          <div class="col text-center">
            <b-card-group deck>
              <b-card
                :title="filme.titulo"
                :img-src="filme.imagem"
                :img-alt="filme.descricao"
                img-top
                tag="article"
                style="max-width: 17rem; height: 40rem"
                class="m-1"
              >
                <b-card-text>
                  {{ filme.descricao }}
                </b-card-text>
                <b-card-text>
                  {{ filme.valor | formatarPreco("R$") }}
                </b-card-text>

     <b-button
              v-if="filme.estoqueDisponivel > 1"
              href="#"
              block
              variant="dark"
              @click="adicionarCarrinho(filme)"
            >
              ALUGAR</b-button
            >
            <b-button
              v-else-if="filme.estoqueDisponivel == 1"
              variant="warning"
              @click="adicionarCarrinho(filme)"
            >
              ÚLTIMA UNIDADE</b-button
            >
            <b-button v-else href="#" block variant="danger" disabled>
              ESGOTADO</b-button
            >
              </b-card>
            </b-card-group>
          </div>
        </div>
      </b-row>
      <b-container v-show="! mostrar">
        <b-row>
          <h3>Meu carrinho:</h3>
        </b-row>
        
      <template>
        <div>
          <b-table striped hover :items="carrinho" :fields="fields"></b-table>
        </div>
      </template>
      
      </b-container>
    </b-container>
  </div>
</template>

<script>
import "bootstrap/dist/css/bootstrap.css";
import "bootstrap-vue/dist/bootstrap-vue.css";
import HelloWorld from "./components/HelloWorld.vue";

export default {
  name: "App",
  components: {
    HelloWorld,
  },
  data() {
    return {
      title: "Locadora de Filmes",
      horas: new Date().getHours(),
      mostrar: false,
      fields: ['id','titulo', 'valor'],
      filmes: [
        {
          id: 1,
          estoqueDisponivel: 3,
          titulo: "Hulk",
          descricao: "Filme Ruim",
          valor: 2,
          imagem: "https://i.imgur.com/0uthCmp.jpg",
        },
        {
          id: 2,
          estoqueDisponivel: 2,
          titulo: "Homem de Ferro",
          descricao: "Homem de Ferro",
          valor: 10,
          imagem: "https://i.imgur.com/OA8pDFM.jpg",
        },
        {
          id: 3,
          estoqueDisponivel: 4,
          titulo: "Thor",
          descricao: "Bonito ",
          valor: 20,
          imagem: "https://i.imgur.com/mt4ZRzw.jpg",
        },
        {
          id: 4,
          estoqueDisponivel: 2,
          titulo: "Capitão América",
          descricao: "Um filme de capitão",
          valor: 40,
          imagem: "https://i.imgur.com/UFmSVtZ.jpg",
        },
        {
          id: 5,
          estoqueDisponivel: 9,
          titulo: "Doutor Estranho",
          descricao: "Magia",
          valor: 10,
          imagem: "https://i.imgur.com/pVEDruM.jpg",
        },
        {
          id: 6,
          estoqueDisponivel: 6,
          titulo: "Pantera Negra",
          descricao: "Um segundo filme de força",
          valor: 10,
          imagem: "https://i.imgur.com/JOSEGKf.jpg",
        },
      ],
      carrinho: [],
    };
  },
  methods: {
    parteDoDia() {
      if (this.horas < 12) {
        return "Está uma ótima manhã para um filme!";
      } else if (this.horas >= 12 && this.horas < 18) {
        return "Que tal uma sessão da tarde agora?";
      } else if (this.horas >= 18) {
        return "Sessão noturna de filmes, partiu?";
      }
    },
    adicionarCarrinho(filme) {
      if (filme.estoqueDisponivel > 0) {
        this.carrinho.filter(c => c.id == filme.id)
        this.carrinho.push(filme);
        filme.estoqueDisponivel -= 1;
      }
    },
  },
  computed: {
    qtCarrinho() {
      return this.carrinho.length;
    },
  },
};
</script>

<style>
body {
  font-family: "Montserrat", sans-serif;
}
.row {
  justify-content: center;
}

.card-deck .card {
  width: 18rem;
  align-content: space-around;
}

.card:hover {
  transform: scale(1.05);
}

.navbar {
  margin-bottom: 2%;
}

table{
  width:100%;
  
}

th{
  text-align: center;
}
</style>
