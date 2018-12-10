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
          <button
            type="button"
            class="btn btn-primary my-2"
            data-toggle="modal"
            data-target="#modalGrafico"
          >Gráfico</button>
        </p>
      </div>
    </div>

    <div id="map"></div>

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

    <!-- Modal -->
    <div
      class="modal fade"
      id="modalGrafico"
      tabindex="-1"
      role="dialog"
      aria-labelledby="modalGrafico"
      aria-hidden="true"
    >
      <div class="modal-dialog" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">
              <b>Gráfico</b>
            </h5>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body">
            <canvas id="grafico" width="400" height="400"></canvas>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-dismiss="modal">Fechar</button>
          </div>
        </div>
      </div>
    </div>

    <div class="container">
      <div v-for="restaurante in restaurantes" :key="restaurante.nome">
        <div class="album py-5 bg-light">
          <div class="container col-4">
            <div class="row">
              <div class="col-sm-12">
                <div class="w3-card-4">
                  <header class="w3-container w3-blue">
                    <h3 align="center">{{ restaurante.nome }}</h3>
                  </header>
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
                      <button
                        type="button"
                        class="btn btn-secondary my-2"
                        @click="removeRestaurante(restaurante.nome)"
                      >Remover</button>
                    </div>
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
            <button type="button" class="btn btn-primary" @click="editaRestaurante">Salvar</button>
          </div>
        </div>
      </div>
    </div>
  </main>

</template>

<style>
  #map {
    height: 100%;
  }
</style>

<script>
var map;
function initMap() {
  map = new google.maps.Map(document.getElementById('map'), {
    center: {lat: -34.397, lng: 150.644},
    zoom: 8
  });
}
</script>

<script src="https://maps.googleapis.com/maps/api/js?key=AIzaSyDQZ3hJqln0I1G1p3smuKIPZYJFPovfJnU&callback=initMap" async defer></script>

<script>
import Chart from 'chart.js';

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
  mounted() {
    for (var key in localStorage) {
      var endereco = localStorage.getItem(key);
      if (endereco != null && endereco != 'INFO')
        this.restaurantes.push({ nome: key, endereco: endereco });
    }
    this.carregaGrafico();
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
    editaRestaurante() {
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
    },
    removeRestaurante(nome) {
      this.nome = nome;
      var restaurante = { nome: this.nome, endereco: this.endereco };
      const indice = this.restaurantes.indexOf(restaurante);
      this.restaurantes.splice(indice, 1);
      localStorage.removeItem(this.nome);
      alert("Restaurante removido com sucesso!");
    },
    carregaGrafico() {
      var estados = [];
	    var numerosPorEstado = [];
	    var cores = [
          'rgba(255, 99, 132, 0.7)',	// Vermelho
          'rgba(54, 162, 235, 0.7)',	// Azul
          'rgba(255, 206, 86, 0.7)',	// Amarelo
          'rgba(75, 192, 192, 0.7)',	// Verde
          'rgba(153, 102, 255, 0.7)', // Roxo
          'rgba(255, 159, 64, 0.7)'	  // Laranja
      ];

      for (var i = 0; i < this.restaurantes.length; i++) {
        estados.push(this.restaurantes[i].nome);
        numerosPorEstado.push(this.restaurantes[i].endereco);
      }

      var ctx = document.getElementById("grafico").getContext('2d');
      var myChart = new Chart(ctx, {
          type: 'horizontalBar',
          data: {
              labels: ["PR", "SP", "SC", "RJ", "MG"],
              datasets: [{
                  label: "Número de Restaurantes",
                  data: [3, 2, 3, 4, 5],
                  backgroundColor: cores,
                  borderColor: cores,
                  borderWidth: 1
              }]
          },
          options: {
              scales: {
                xAxes: [{
                  ticks: {
                    min: 0
                  }
                }],
              },
              responsive: true,
              legend: { position: 'top' },
              animation: { animateScale: true, animateRotate: true },
              tooltips: {
                  callbacks: {
                    label: function(tooltipItem, data) {
                      var dataset = data.datasets[tooltipItem.datasetIndex];
                      var total = dataset.data.reduce(function(previousValue, currentValue, currentIndex, array) {
                        return previousValue + currentValue;
                      });
                      var currentValue = dataset.data[tooltipItem.index];
                      var percentage = parseFloat((currentValue/total) * 100).toFixed(2);
                      return percentage + "%";
                    }
                  }
              }
          }
      });
    },
  }
};
</script>
