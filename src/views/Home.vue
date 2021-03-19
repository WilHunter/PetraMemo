<template>
  <v-container class=" corpo text-center">
    <h1>JOGO PETRAMEMO</h1>
    <v-form>
      <v-container class="text-center">
        <v-row>
          <v-col
            cols="12"
            md="4"
          >
            <v-text-field
              required
              color="#002752"
              loading
              clearable
              placeholder="Insira o seu Nome..."
              v-model="nome"
              :disabled="desabilitado"
              style="background-color: currentColor;color:#A89968;border-radius:1%"
            >
            </v-text-field>
          </v-col>
        </v-row>
        <v-row>
          <v-col
            cols="12"
            md="4"
            class="placar"
          >
            <p>Jogador: <b>{{ this.nome }}</b></p>
            <p>Tempo: <b>{{ this.tempo }}</b></p>
            <p>Rodadas: <b>{{ this.rodadas }}</b></p>
          </v-col>
        </v-row><br>
        <v-btn class="botao" type="button" @click="comecar()" :disabled="desabilitado || nome == ''">Jogar</v-btn>
        <v-btn class="botao" type="button" @click="reiniciar()" :disabled="!desabilitado"> Reiniciar</v-btn>
      </v-container>
    </v-form>
    <div>
      <v-container class="cartas" v-if="!pararJogo">
          <div class="carta" 
            v-for="(carta,i) in cartas" 
            :key="i"
            :class="{ virada: carta.virada, correta: carta.correta}" @click="virarCarta(carta)">
            <div class="costas"></div>
            <span class="frente" v-bind:style="{ backgroundImage: 'url(' + carta.src + ')' }"></span>
          </div>
      </v-container>
      <div class="info container" v-if="mostrarResultado">
        <v-card 
          class="card" 
          v-if="mostrarFinal"
          elevation="20"
          style="background-color:#012b58">
          <div class="well">
            <div class="page-header">
              <h1>Parabéns!!  Você ganhou.</h1>
            </div>
            <div class="saldo">Tempo: {{ tempo }}</div>
            <div class="saldo">Rodadas: {{ rodadas }}</div>
          </div>
          <v-container class="resultados">
            <h1>PLACAR</h1>
            <v-simple-table 
              height="200px"               
              style="background-color:#012b58;width:90%" >
              <thead >
                <tr>
                  <th style="color:#A89968">Jogador</th>
                  <th style="color:#A89968">Tempo</th>
                  <th style="color:#A89968">Rodadas</th>
                </tr>
              </thead>
              <tbody style="color:#A89968">
                <tr v-for="(jogador,i) in ordenarResultados" :key="i">
                  <td>{{ jogador.nome }}</td>
                  <td>{{ jogador.tempo }}</td>
                  <td>{{ jogador.rodadas }}</td>
                </tr>
              </tbody>
            </v-simple-table>
            <div>
              <v-btn class="botao" elevation="20" @click="reiniciar()" >Jogar novamente!</v-btn>
            </div>
          </v-container>
        </v-card>
      </div>
    </div>
    <router-view></router-view>
  </v-container>
</template>

<script>
let cartas =  [{src:'https://i1.wp.com/jornalibia.com.br/wp-content/uploads/2018/03/Fruta-do-conde.jpg?fit=640%2C477&ssl=1',name: 1 },
              {src:'https://saberhortifruti.com.br/wp-content/uploads/2020/09/maracuja.jpg',name: 2 },
              {src:'https://saberhortifruti.com.br/wp-content/uploads/2019/03/Pitaya.jpg',name: 3 },
              {src:'https://www.dicio.com.br/upload/qu/iv/quivi-cke.jpg',name: 4 },
              {src:'https://static.todamateria.com.br/upload/ab/ac/abacate-cke.jpg',name: 5 },
              {src:'https://www.spdm.org.br/media/k2/items/cache/cd37ed46198bb78c38b3e24a7fc57fa1_XL.jpg',name: 6 },
              {src:'https://static.todamateria.com.br/upload/fr/ut/fruta39pera-cke.jpg',name: 7 },
              {src:'https://www.portalbueno.com.br/uploads/cache/23-12-2017-061117-77-690x460-0c9054ef-portalbueno.webp',name: 8 },
              {src:'https://saude.abril.com.br/wp-content/uploads/2016/11/mac3a7c3a3.jpg',name: 9 },
              {src:'https://cptstatic.s3.amazonaws.com/imagens/enviadas/materias/materia27506/fruta-de-mercado-comercial-garantido-melao-cpt.jpg',name: 10}
              ];

let embaralhar = () => {

  let baralho = [].concat(_.cloneDeep(cartas), _.cloneDeep(cartas));

  return _.shuffle(baralho);

}

export default {
  name: 'Home',
  
  data: () => ({
    nome: '',
    pararJogo: true,
    desabilitado: false,
    cronometro: 0,
    tempo: 0,
    cartas: [],
    rodadas: 0,
    combinacoes: 0,
    desvirarTempo: 0,
    mostrarResultado: false,
    mostrarFinal: false,
    jogadores: []
      
  }),
  methods: {
    

    reiniciar() {

      this.mostrarResultado = false;

      this.mostrarFinal = false;

      this.pararJogo = true;

      this.desabilitado = false;

      this.tempo = 0;

      this.rodadas = 0;

      clearInterval(this.cronometro);

      this.cronometro = 0;

      let cartas = embaralhar();   

      _.each(cartas, (carta) => {

          carta.virada = false;

          carta.correta = false;

      });      

      this.cartas = cartas;	

	},
		
	comecar() {

      this.mostrarResultado = false;

      this.mostrarFinal = false;

      this.pararJogo = false;

      this.desabilitado = true;

      this.cronometro = setInterval(() => {

          this.tempo++;

    }, 1000);

		},
    
    fim() {

      this.pararJogo = true;

      clearInterval(this.cronometro);

      this.mostrarResultado = true;

      this.mostrarFinal = true;

      let jogador = {
        nome: this.nome,
        tempo: this.tempo,
        rodadas: this.rodadas
      };

      this.jogadores.push(jogador);

    },

    cartasViradas() {

      return _.filter(this.cartas, carta => carta.virada);

    },

    mesmaCartaVirada() {

      let cartasViradas = this.cartasViradas();

      if (cartasViradas.length == 2) {

        if (cartasViradas[0].name == cartasViradas[1].name) {

            return true;

        }

      }

    },

    deixarCartaVirada() {

      _.each(this.cartas, (carta) => {

        if (carta.virada) {

          carta.correta = true;

        }

      });

    },

    verificarTodasCorretas() {

      let cartasEncontradas = _.filter(this.cartas, carta => carta.correta);

      this.combinacoes = cartasEncontradas.length;

      if (cartasEncontradas.length == this.cartas.length) {

          return true;

      }

    },
    
    virarCarta(carta) {

      if (carta.correta || carta.virada) return;
      
      let contador = this.cartasViradas().length;

      if (contador == 0) {

          carta.virada = !carta.virada;

      } else if (contador == 1) {

        carta.virada = !carta.virada;

        this.rodadas++;

        if (this.mesmaCartaVirada()) {

          this.flipBackTimer = setTimeout( ()=> {

            this.limparDesvirarTempo();

            this.deixarCartaVirada();

            this.desvirar();

            if (this.verificarTodasCorretas()) {

                this.fim();

            }   

          }, 200);

        } else {

          this.desvirarTempo = setTimeout( ()=> {

              this.limparDesvirarTempo();

              this.desvirar();

          }, 1000);

        }

      }

    },

    limparDesvirarTempo() {

      clearTimeout(this.desvirarTempo);

      this.desvirarTempo = null;

    },
    
    desvirar() {

        _.map(this.cartas, carta => carta.virada = false);

    },
      
	},

  computed: {

    ordenarResultados: function() {

      function compare(a, b) {

        if (a.rodadas < b.rodadas) {

          return -1;

        }

        if (a.rodadas > b.rodadas) {

          return 1;

        }

        return 0;

      }

      return this.jogadores.sort(compare);

    }

  },

	created() {

		this.reiniciar();

	}
}
</script>

<style scoped>
  .corpo{
    text-align: center;
    padding: 2em;
  }
  .corpo h1{
    color:#A89968;
    animation: cabeca 3s ease ;
  }
  @keyframes cabeca{
    from{
      opacity: 0;
    }
    to{
      opacity: 1;
    }
  }
  input #input-21{
    color:#A89968!important
  }
  .placar{
    color: #A89968;
    font-size: 1.5em;
    animation: score 3s ease-in;
  }
  @keyframes score{
  from{
      opacity: 0;
    }
    to{
      opacity: 1;
    }
  }
  .botao{
    background-color: transparent!important;
    color: #A89968!important;
    margin-right: 1em;
  }
  .cartas{
    animation: cartas 4s ease;
    margin-bottom: 3em auto!important;
  }
  @keyframes cartas{
  from{
      opacity: 0;
    }
    to{
      opacity: 1;
    }
  }
  .cartas .carta {
    position: relative;
    display: inline-block;
    width: 218px;
    height: 127px;
    margin: 5px;
    transition: opacity 0.3s;
  }
    .cartas .carta .frente, .cartas .carta .costas {
    border-radius: 10px;
    position: absolute;
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
    width: 100%;
    height: 100%;
    backface-visibility: hidden;
    transform: translateZ(0);
    transition: transform 0.5s;
    transform-style: preserve-3d;
  }

  .cartas .carta .costas {
    background-color: #053a72;
    background-image: url("../assets/petragold.svg");
    background-size: 75%;
    background-position: center;
    background-repeat: no-repeat;
  }

  .cartas .carta .frente {
    transform: rotateY(180deg);
    background-size: cover;
    background-repeat: no-repeat;
    background-position: center;
  }

  .cartas .carta.virada .costas, .cartas .carta.correta .costas {
    transform: rotateY(180deg);
  }

  .cartas .carta.virada .frente, .cartas .carta.correta .frente {
    transform: rotateY(0deg);
  }

  .cartas .carta.correta {
    opacity: 0.3;
  }
  .resultados{
    color:#A89968;
    margin: 1em;
  }
  .saldo{
    color:#A89968;
    margin: 2em;
  }
  .card{
    padding: 1.5em;
  }
</style>