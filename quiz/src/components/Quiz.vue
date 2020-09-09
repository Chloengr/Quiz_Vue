<template>
  <div class="container">
    <section v-if="homePart">
      <h1>Bienvenue sur Trivia Quiz</h1>
      <h3>Choississez un thême et un nombre de questions :</h3>
      <div class="choice">
        <div class="theme">
          <p>Quel thême ?</p>
          <select v-model="category">
            <option value="10">Littérature</option>
            <option value="11">Films</option>
            <option value="12">Musique</option>
            <option value="13">Théâtre et Musicals</option>
            <option value="14">Télévision</option>
            <option value="15">Jeux Vidéo</option>
            <option value="16">Jeux de Société</option>
            <option value="17">Science et Nature</option>
          </select>
        </div>
        <div class="number">
          <p>Combien de questions ?</p>
          <input type="number" v-model="amount" min="1" max="30">
        </div>
      </div>
      <button @click="startQuiz">C'est parti !</button>
    </section>

    <section v-if="questionsPart">
      <h1>Question n°{{currentQuestion}} sur {{amount}}</h1>
      <!-- <p v-for="q in questionTitle" v-bind:key="q.id">{{q.question}}</p> -->
      <p>{{questionTitle[currentQuestion-1].question}}</p>
      <button v-on:click="sendAnswer('True')">Vrai</button>
      <button v-on:click="sendAnswer('False')">Faux</button>
      <button @click="goBack">Recommencer</button>
    </section>

    <section v-if="scorePart">
      <h1>C'est fini !</h1>
      <p>Vous avez eu {{score}} sur {{amount}}</p>
      <button @click="goBack">Recommencer</button>
    </section>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Quiz',
  created(){
    this.homePart = true;
  },
  data(){
    return {
      homePart: false,
      questionsPart: false,
      scorePart: false,
      amount: 1,
      category: 10,
      currentQuestion: 1,
      questionTitle: [],
      score: 0,
      correctAnswers:0
    }
  },
  methods: {
    handleData(){
      axios
        .get('https://opentdb.com/api.php?amount='+this.amount+'&category='+this.category+'&type=boolean', {
          headers: { 'Content-Type': 'application/x-www-form-urlencoded'}
          }
        )
        .then(res => {
          this.questionTitle = res.data.results
        })
    },
    startQuiz(){
      this.handleData()
      this.homePart = false
      this.questionsPart = true
    },
    sendAnswer(output){
      console.log('My answer:' + output);
      console.log(this.questionTitle[this.currentQuestion-1].correct_answer);
      if((this.currentQuestion) < this.amount){
        if(this.questionTitle[this.currentQuestion-1].correct_answer === output)
          this.score=+1;

        this.currentQuestion++
      }
      else {
        this.questionsPart = false;
        this.scorePart = true;
      }
    },
    goBack(){
      this.handleData(),
      this.homePart = true,
      this.questionsPart = false,
      this.scorePart = false,
      this.score = 0,
      this.currentQuestion = 1
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
