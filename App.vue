<template>
  <div>
    <b-navbar type="dark" variant="dark">
      <b-navbar-nav>
        <b-nav-item @click="mostrarFilmes = true">Home</b-nav-item>
        <b-nav-item @click="mostrarFilmes = false">Carrinho</b-nav-item>
      </b-navbar-nav>
    </b-navbar>
    <b-container>
      <b-row>
        <h1>{{ title }}</h1>
      </b-row>
      <b-row>
        <HelloWorld msg="" />
      </b-row>
      <b-row v-show="mostrarFilmes">
        <p>Carrinho: {{ quantidadeNoCarrinho }} filmes</p>
      </b-row>
      <b-row v-show="mostrarFilmes">
        <div class="cards">
          <b-card
            :key="filme.id"
            v-for="filme in ordenar(filmes)"
            :id="filme.id"
            :title="filme.titulo"
            :img-src="filme.imagem"
            :img-alt="filme.descricao"
            img-top
            tag="article"
            style="max-width: 18rem"
            class="mb-3 text-center"
          >
            <b-card-text>
              {{ filme.descricao }}
            </b-card-text>
            <b-card-text> R$ {{ filme.valor }},00 </b-card-text>

            <b-card-text >
              <p>Avaliação:</p>
              <b-icon icon="star-fill" variant="warning" v-for="estrelas in filme.avaliacao" :key="estrelas"></b-icon>
              <b-icon icon="star" variant="warning" v-for="estrelas_vazadas in 5-filme.avaliacao" :key="'star'+estrelas_vazadas"></b-icon>
              
            </b-card-text>

            <b-button
              v-if="filme.estoqueDisponivel > 1"
              href="#"
              block
              variant="dark"
              @click="adicionarAoCarrinho(filme)"
            >
              ALUGAR</b-button
            >
            <b-button
              v-else-if="filme.estoqueDisponivel == 1"
              variant="warning"
              @click="adicionarAoCarrinho(filme)"
            >
              ÚLTIMA UNIDADE</b-button
            >
            <b-button v-else href="#" block variant="danger" disabled>
              ESGOTADO</b-button
            >
          </b-card>
        </div>
      </b-row>
      <b-row v-show="!mostrarFilmes">
        <b-row class="table">
          <h3>Resumo de pedido</h3>
          <b-table
            block
            striped
            hover
            dark
            responsive
            head-variant="light"
            :items="carrinho"
            :fields="fields"
          ></b-table>
          <b-row>
            <p>Total : R${{ somaPedido }},00</p></b-row
          >
        </b-row>
      <b-row>
        <b-button variant="dark" @click="continuaCompra = false">Comprar</b-button>
      </b-row>
      </b-row>
        <b-form block v-show="!mostrarFilmes">
          <div v-show="!continuaCompra">
            <div class="form-group">
            <label for="pedido.primeiroNome">Primeiro nome</label>
            <input
              type="text"
              class="form-control"
              id="primeiroNome"
              placeholder="Digita o primeiro nome"
              v-model.trim.lazy="pedido.primeiroNome"
            />
          </div>
          <div class="form-group">
            <label for="ultimoNome">Último nome</label>
            <input
              type="text"
              class="form-control"
              id="ultimoNome"
              placeholder="Digite o último nome"
              v-model.trim.lazy="pedido.ultimoNome"
            />
          </div>
          <div class="form-group">
            <label for="endereco">Endereço</label>
            <input
              type="text"
              class="form-control"
              id="endereco"
              placeholder="Digita o endereço"
              v-model.trim.lazy="pedido.endereco"
            />
          </div>
          <div class="form-group">
            <label for="cidade">Cidade</label>
            <input
              type="text"
              class="form-control"
              id="cidade"
              placeholder="Digita a cidade"
              v-model.trim.lazy="pedido.cidade"
            />
          </div>
          <div class="form-group">
            <label for="estado">Estado</label>
            <select class="form-control" id="estado" v-model="pedido.estado">
              <!-- <option disabled value>Escolha um estado</option> -->
              <option v-for="(estados, index) in estados" :key="index">{{ estados }}</option>
            </select>
          </div>
          <div class="form-group">
            <label for="cep">CEP</label>
            <input
              type="number"
              class="form-control"
              id="cep"
              placeholder="Digita o CEP"
              v-model.number="pedido.cep"
            />
          </div>
          <div class="form-group form-check">
            <input
              type="checkbox"
              class="form-check-input"
              id="pagoNaEntrega"
              value="true"
              v-bind:true-value="pedido.simEntrega"
              v-bind:false-value="pedido.naoEntrega"
              v-model="pedido.pagoNaEntrega"
            />
            <label class="form-check-label" for="pagoNaEntrega"
              >Pago na entrega?</label
            >
          </div>
          <div class="form-group form-check-inline">
            <input
              type="radio"
              class="form-check-input"
              id="manha"
              value="Manhã"
              v-model="pedido.entrega"
            />
            <label class="form-check-label" for="manha">Manhã</label>
          </div>
          <div class="form-group form-check-inline">
            <input
              type="radio"
              class="form-check-input"
              id="tarde"
              value="Tarde"
              v-model="pedido.entrega"
            />
            <label class="form-check-label" for="tarde">Tarde</label>
          </div>
          <div class="form-group form-check-inline">
            <input
              type="radio"
              class="form-check-input"
              id="noite"
              value="Noite"
              v-model="pedido.entrega"
            />
            <label class="form-check-label" for="noite">Noite</label>
          </div>
        <b-row v-show="!continuaCompra">
        <h4>Confirmar informações do Pedido: </h4>
        <pre>
          Primeiro nome: {{ pedido.primeiroNome }}
          Último nome: {{ pedido.ultimoNome }}
          Endereço: {{ pedido.endereco }}
          Cidade: {{ pedido.cidade }}
          Estado: {{ pedido.estado }}
          CEP: {{ pedido.cep }}
          Pago na entrega?: {{ pedido.pagoNaEntrega }}
          Entrega: {{ pedido.entrega }}
        </pre>
      </b-row>
        <div class="col text-center" v-show="!continuaCompra">
          <div class="form-group">
            
            <button
              type="submit"
              class="btn btn-dark"
              v-on:click="submitFormulario">
              Finalizar Compra
            </button>
            </div>
          </div>
          </div>
        
        </b-form>
      
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
      carrinho: [],
      qtPreview: [],
      fields: ["titulo", "preço", "quantidade"],
      mostrarFilmes: true,
      continuaCompra: true,
      filmes: [
        {
          id: 1,
          estoqueDisponivel: 3,
          titulo: "Hulk",
          descricao: "Filme Ruim",
          valor: 2,
          avaliacao: 2,
          imagem: "https://i.imgur.com/0uthCmp.jpg",
        },
        {
          id: 2,
          estoqueDisponivel: 2,
          titulo: "Homem de Ferro",
          descricao: "Homem de Ferro",
          valor: 10,
          avaliacao: 4,
          imagem: "https://i.imgur.com/OA8pDFM.jpg",
        },
        {
          id: 3,
          estoqueDisponivel: 4,
          titulo: "Thor",
          descricao: "Bonito ",
          valor: 20,
          avaliacao: 3,
          imagem: "https://i.imgur.com/mt4ZRzw.jpg",
        },
        {
          id: 4,
          estoqueDisponivel: 2,
          titulo: "Capitão América",
          descricao: "Um filme de capitão",
          valor: 40,
          avaliacao: 4,
          imagem: "https://i.imgur.com/UFmSVtZ.jpg",
        },
        {
          id: 5,
          estoqueDisponivel: 9,
          titulo: "Doutor Estranho",
          descricao: "Magia",
          valor: 10,
          avaliacao: 5,
          imagem: "https://i.imgur.com/pVEDruM.jpg",
        },
        {
          id: 6,
          estoqueDisponivel: 6,
          titulo: "Pantera Negra",
          descricao: "Um segundo filme de força",
          valor: 10,
          avaliacao: 4,
          imagem: "https://i.imgur.com/JOSEGKf.jpg",
        },
      ],
      estados:{
        RJ: 'Rio de Janeiro',
        MG: 'Minas Gerais',
        SP: 'São Paulo',
        ES: 'Espirito Santo'
      },
      pedido: {
        primeiroNome: "",
        ultimoNome: "",
        endereco: "",
        cidade: "",
        estado: "",
        cep: "",
        pagoNaEntrega: "Não",
        simEntrega: "Sim",
        naoEntrega: "Não",
        entrega: "Manhã",
      },
    };
  },
  methods: {
    parteDoDia() {
      if (this.horas < 12) {
        return "Está de dia!";
      } else if (this.horas >= 12 && this.horas < 18) {
        return "Está de tarde!";
      } else if (this.horas >= 18) {
        return "Está de noite!";
      }
    },
    adicionarAoCarrinho: function (filme) {
      if (filme.estoqueDisponivel > 0) {
        this.qtPreview.push(filme);
        let indexFilme = this.carrinho.findIndex((obj) => obj.id == filme.id);
        filme.quantidade = (filme.quantidade || 0) + 1;
        if (indexFilme == -1) {
          filme.preço = `R$${filme.valor},00`;
          this.carrinho.push(filme);
        } else {
          this.carrinho.splice(indexFilme, 1, filme);
        }
        filme.estoqueDisponivel -= 1;
      }
    },
    submitFormulario() {
      alert("Pedido finalizado");
    },
    ordenar: function() {
      return [...this.filmes].sort((a,b) => {
        return (a.titulo < b.titulo) ? - 1 : (a.titulo > b.titulo) ? 1 : 0;
      })
    }
  },
  computed: {
    quantidadeNoCarrinho: function () {
      return this.qtPreview.length;
    },
    // somaPedido(){
    //   let total = 0
    // for (let filme of this.carrinho){
    //   total += (filme.quantidade * filme.valor)
    // }
    // return total
    // }

    somaPedido: function () {
      return this.carrinho.reduce(function (accumulator, filme) {
        return accumulator + filme.valor * filme.quantidade;
      }, 0);
    },
  }
};
</script>

<style>
.table {
  width: 100%;
}
.container {
  font-family: "Quicksand", sans-serif;
  margin-top: 40px;
}
.row {
  justify-content: center;
}
.cards {
  display: flex;
  flex-wrap: inherit;
  justify-content: space-around;
}
.card:hover {
  transform: scale(1.05);
}

pre{
  width: 100%;
}
</style>


