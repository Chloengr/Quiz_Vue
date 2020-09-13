<template>
  <div class="container">
    <section v-if="homePart">
      <h1>Bienvenue sur Trivia Quiz</h1>
      <h3>Choississez un thême et un nombre de questions :</h3>
      <div class="choice">
        <div class="theme">
          <p>Quel thême ?</p>
          <select class="btn selectBtn" v-model="category">
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
          <input class="btn selectBtn" type="number" v-model="amount" min="1" max="30">
        </div>
      </div>
      <button class="btn startBtn" @click="startQuiz">C'est parti !</button>
    </section>

    <section v-if="questionsPart">
      <h1>Question n°{{b}} sur {{amount}}</h1>
      <p class="question" v-for="q in questions.slice(a,b)" :key="q.id">
        {{ decodeHtml(q.question)}}
      </p>
      <div>
        <button class="btn" v-on:click="sendAnswer('True')">Vrai</button>
        <button class="btn" v-on:click="sendAnswer('False')">Faux</button>
      </div>
      <button class="btn startBtn" @click="goBack">Recommencer</button>
    </section>

    <section v-if="scorePart">
      <h1>C'est fini !</h1>
      <p>Vous avez eu {{score}} sur {{amount}}</p>
      <button class="btn startBtn" @click="goBack">Recommencer</button>
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
      a:0,
      b:1,
      homePart: false,
      questionsPart: false,
      scorePart: false,
      amount: 1,
      category: 10,
      questions: [],
      score: 0,
      correctAnswers:0
    }
  },
  methods: {
    decodeHtml(html) {
      var txt = document.createElement("textarea");
      txt.innerHTML = html;
      return txt.value;
    },
    goBack(){
      this.handleData(),
      this.homePart = true,
      this.questionsPart = false,
      this.scorePart = false,
      this.score = 0,
      this.a = 0
      this.b = 1
    },
    handleData(){
      axios
        .get('https://opentdb.com/api.php?amount='+this.amount+'&category='+this.category+'&type=boolean', {
          headers: { 'Content-Type': 'application/json;charset=utf-8'}
          }
        )
        .then(res => {
          this.questions = res.data.results
        })
    },
    sendAnswer(output){
      if(this.b < this.amount){
        if(this.questions[this.a].correct_answer === output) {
          this.score++;
        }
        this.a++,
        this.b++
      }
      else if(this.b == this.amount){
        if(this.questions[this.a].correct_answer === output) {
          this.score++;
        }
        this.questionsPart = false;
        this.scorePart = true;
      }
      else{
        this.questionsPart = false;
        this.scorePart = true;
      }
    },
    startQuiz(){
      this.handleData()
      this.homePart = false
      this.questionsPart = true
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h3{
  margin: 20px;
}
.btn{
  border: none;
  margin: 10px;
  padding: 15px 35px;
  text-align: center;
  transition: 0.5s;
  background-size: 200% auto;
  box-shadow: 0 0 15px rgb(182, 182, 182);
  border-radius: 10px;
  font-size: 17px;
}
.choice{
  display: flex;
  align-items: center;
  justify-content: center;
}
.startBtn{
  color: white;
  background-image: linear-gradient(to right, #f6d365 0%, #fda085 51%, #f6d365 100%);
}
.question{
  margin: 50px;
}
</style>
