<template>
  <div>
    <ScoreBoard :winCount="this.winCount" :loseCount="loseCount"/>
    <template v-if="this.question">
      <h1 v-html="this.question">
      </h1>
      <template v-for="(answer, index) in this.answers" :key="index"> 
        <input
          :disabled="this.answerSubmitted"
          type="radio"
          name="options" 
          :value="answer"
          v-model="this.chosen_answer"
          >

        <label v-html="answer"></label><br>
      </template>
      <button class="send" type="button" @click="this.submitAnswer()" v-if="!this.answerSubmitted">Send</button>
      <section calss="result" v-if="this.answerSubmitted">
        <h4 v-if="this.chosen_answer == this.correctAnswer" v-html="'&#9989; Correct the answer is ' + this.correctAnswer">
        </h4>
        <h4 v-else>
          &#10060; Your answer is wrong, the correct answer is "{{ this.correctAnswer }}"
        </h4>
        <button class="send" type="button" @click="getNewQuestion()" v-if="this.answerSubmitted">Next Question</button>
      </section>
    </template>
  </div>
</template>

<script>

import ScoreBoard from '@/components/ScoreBoard.vue'

export default {
  name: 'App',
  components: {
    ScoreBoard
  },
  data() {
    return {
      question: undefined,
      incorrectAnswers: undefined,
      correctAnswer: undefined,
      chosen_answer: undefined,
      answerSubmitted: false,
      winCount: 0,
      loseCount: 0
    }
  },
  computed: {
    answers() {
      var answers = [...this.incorrectAnswers];//to copy an instance of the array [...this.array]
      answers.splice(Math.round(Math.random() * answers.length), 0, this.correctAnswer);
      return answers;
    }
  },
  methods: {
    submitAnswer() {
      if (!this.chosen_answer) {
        alert('Pick one of the options')
      } else {
        this.answerSubmitted = true;
        if (this.chosen_answer == this.correctAnswer) {
          this.winCount++;
        } else {
          this.loseCount++;
        }
      }
    },
    getNewQuestion() {
      this.answerSubmitted = false;
      this.chosen_answer = undefined;
      this.question = undefined;
      this.axios
      .get('https://opentdb.com/api.php?amount=1&category=18&difficulty=easy')
      .then((response) => {
        this.question = response.data.results[0].question;
        this.incorrectAnswers = response.data.results[0].incorrect_answers;
        this.correctAnswer = response.data.results[0].correct_answer;
      })
    }
  },
  created() {
      this.axios
      .get('https://opentdb.com/api.php?amount=1&category=18&difficulty=easy')
      .then((response) => {
        this.question = response.data.results[0].question;
        this.incorrectAnswers = response.data.results[0].incorrect_answers;
        this.correctAnswer = response.data.results[0].correct_answer;
      })
  }
}

//https://opentdb.com/api.php?amount=1&category=18&difficulty=hard

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
