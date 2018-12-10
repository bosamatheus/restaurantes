<template>
  <main role="main">
    <div class="jumbotron text-center">
      <div class="container">
        <h1 class="jumbotron-heading">Não lembra o nome daquele restaurante especial?</h1>
        <p class="lead text-muted">Aqui no Comidarada você encontra!</p>
        <p>
          <button
            type="button"
            class="btn btn-primary my-2"
            data-toggle="modal"
            data-target="#modalCadastro"
          >Cadastrar</button>
        </p>
      </div>
    </div>

    <!-- Modal -->
    <div
      class="modal fade"
      id="modalCadastro"
      tabindex="-1"
      role="dialog"
      aria-labelledby="modalCadastro"
      aria-hidden="true"
    >
      <div class="modal-dialog" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">
              <b>Cadastro de Restaurante</b>
            </h5>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body">
            <div class="row">
              <div class="col-sm-12">
                <label for="novoNome">Nome:</label>
                <input id="novoNome" type="text" v-model="novoNome">
              </div>
            </div>
            <div class="row">
              <div class="col-sm-12">
                <label for="novoEndereco">Endereço:</label>
                <input id="novoEndereco" type="text" v-model="novoEndereco">
              </div>
            </div>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-dismiss="modal">Fechar</button>
            <button type="button" class="btn btn-primary" @click="cadastraRestaurante">Salvar</button>
          </div>
        </div>
      </div>
    </div>

    <div class="container">
      <div v-for="restaurante in restaurantes" :key="restaurante.nome">
        <div class="album py-5 bg-light">
          <div class="container">
            <div class="row">
              <div class="col-md-4">
                <div class="w3-card-4">
                  <header class="w3-container w3-blue">
                    <h3 align="center">{{ restaurante.nome }}</h3>
                  </header>
                  <br>

                  <div class="w3-container">
                    <div class="row">
                      <div class="col-sm-12">
                        <p>- Endereço: {{ restaurante.endereco }}</p>
                      </div>
                    </div>
                  </div>

                  <footer class="w3-container w3-center">
                    <div class="btn-group">
                      <button
                        type="button"
                        class="btn btn-primary my-2"
                        @click="carregaModalEdicao(restaurante.nome)"
                      >Editar</button>
                    </div>
                    <br>
                    <br>
                  </footer>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div
      class="modal fade"
      id="modalEdicao"
      tabindex="-1"
      role="dialog"
      aria-labelledby="modalEdicao"
      aria-hidden="true"
    >
      <div class="modal-dialog" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">
              <b>Editar: {{ nomeAnterior }}</b>
            </h5>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body">
            <div class="row">
              <div class="col-sm-12">
                <label for="nome">Nome:</label>
                <input id="nome" type="text" v-model="nome">
              </div>
            </div>
            <div class="row">
              <div class="col-sm-12">
                <label for="endereco">Endereço:</label>
                <input id="endereco" type="text" v-model="endereco">
              </div>
            </div>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-dismiss="modal">Fechar</button>
            <button type="button" class="btn btn-primary" @click="editarRestaurante">Salvar</button>
          </div>
        </div>
      </div>
    </div>
  </main>

</template>

<script>
import places from 'places.js';
var placesAutocomplete = places({
  appId: "AAY4FMWXZA",
  apiKey: "364fd56fc021b2a42d2b9d9b8f6ee03c",
  container: document.querySelector('#address-input')
});

export default {
  name: "Restaurantes",
  data() {
    return {
      novoNome: "",
      novoEndereco: "",
      restaurantes: [],
      nomeAnterior: "",
      nome: "",
      endereco: ""
    };
  },
  methods: {
    cadastraRestaurante() {
      var restaurante = { nome: this.novoNome, endereco: this.novoEndereco };
      this.restaurantes.push(restaurante);
      localStorage.setItem(restaurante.nome, restaurante.endereco);
      alert("Restaurante cadastrado com sucesso!");
      this.novoNome = "";
      this.novoEndereco = "";
    },
    carregaModalEdicao(nome) {
      this.nome = nome;
      this.nomeAnterior = this.nome;
      this.endereco = localStorage.getItem(this.nome);
      $('#modalEdicao').modal();
    },
    editarRestaurante() {
      // Remove o elemento
      var restauranteAnterior = { nome: this.nomeAnterior, endereco: this.endereco };
      const indice = this.restaurantes.indexOf(restauranteAnterior);
      this.restaurantes.splice(indice, 1);
      localStorage.removeItem(this.nomeAnterior);
      // Cadastra o novo elemento
      var restaurante = { nome: this.nome, endereco: this.endereco };
      this.restaurantes.push(restaurante);
      localStorage.setItem(restaurante.nome, restaurante.endereco);
      alert("Restaurante editado com sucesso!");
    }
  }
};
</script>
