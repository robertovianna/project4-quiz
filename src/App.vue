<template>

  <div>
    
    <ScoreBoard />

    <template v-if="this.question">
      <!-- uma das formas de exibir a pergunta na tela é com {{ this.question }}-->
      <!-- <h1 v-html="this.question"> para ler o html de forma correta -->
      <h1 v-html="this.question">
      </h1>
      <!--Um novo elemento pai que não vai ser visível no html-->
      <template v-for="(answer, index) in this.answers" :key="index">
        <input :disabled="this.answerSubmitted" type="radio" name="options" :value="answer"
          v-model="this.chosen_answer">

        <label v-html="answer"></label><br>
      </template>

      <button @click="this.submitAnswer()" class="send" type="button">Send</button>


      <section class="result" v-if="this.answerSubmitted">
        <template v-if="this.chosen_answer == this.correctAnswer">
          <h4>&#9989; Parabéns, a resposta "{{ this.correctAnswer }}" está correta.</h4>
        </template>
        <template v-else>
          <h4>&#10060; Que pena, a resposta está errada. A resposta correta é "{{ this.correctAnswer }}".</h4>
        </template>
        <button @click="this.getNewQuestion()" class="send" type="button">Próxima pergunta</button>
      </section>



    </template>

  </div>

</template>

<script>

import ScoreBoard from '@/components/ScoreBoard.vue'

export default {

  name: 'App',
  components:{
    ScoreBoard 
  },
  //função para retornar os dados
  data() {
    //criar as propriedades (questões, respostas corretas,respostas incorretas)
    //chosen_answer para enviar ao console a resposta marcada
    //ansewerSubmitted controla se ja enviou alguma resposta ou não (será usado para desabilitar o botão apos envio)
    return {
      question: undefined,
      incorrectAnswers: undefined,
      correctAnswer: undefined,
      chosen_answer: undefined,
      answerSubmitted: false,
    }

  },
  //computed Properties substitue os methods
  //parse transforma  string em json e o stringfy transforma o objeto em string
  computed: {
    answers() {
      var answers = JSON.parse(JSON.stringify(this.incorrectAnswers));
      //splice para adicionar elementos (respostas) de maneira aleatória
      //Math.random() não retorna un numero inteiro, sempre um numero entre 0 e 1
      //Math.round para arredondar o numero
      answers.splice(Math.round(Math.random() * answers.length), 0, this.correctAnswer);
      return answers;
    }

  },
  methods: {
    submitAnswer() {
      //se no chosen_answer não tiver nada, exibir o alert
      if (!this.chosen_answer) {
        alert('Escolha uma das opções');
      } else {
        this.answerSubmitted = true;
        if (this.chosen_answer == this.correctAnswer) {
          console.log('Você acertou');
        } else {
          console.log('Você errou');
        }
      }
    },

    getNewQuestion() {
       
      //quando passar para a proxima questão, os metodos answer,chosen e question 
      //serão reiniciados para não exibir dados repetidos
      this.answerSubmitted = false;
      this.chosen_answer = undefined;
      this.question = undefined;
     

     //fazendo a requisição com o axios
     // Created faz com que a requisição get  com axios seja executada assim que a pagina é iniciada
      this.axios
        //url da api
        .get('https://opentdb.com/api.php?amount=1&category=18')
        .then((response) => {
          this.question = response.data.results[0].question;
          this.incorrectAnswers = response.data.results[0].incorrect_answers;
          this.correctAnswer = response.data.results[0].correct_answer;
        }).catch((erro) => {
          //catch é utilizado para manipular os erros da requisição
          alert('pagina indisponivel no momento');
          console.error(erro + "ao consumir a api");
        })

    }

  },
  created() {
    this.getNewQuestion();
  },

}

</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
  max-width: 960px;

  input[type=radio] {
    margin: 12px 4px;
  }

  button.send {
    margin-top: 12px;
    height: 40px;
    min-width: 120px;
    padding: 0 16px;
    color: #fff;
    background-color: #1867c0;
    border: 1px solid #1867c0;
    cursor: pointer;
  }
}
</style>
