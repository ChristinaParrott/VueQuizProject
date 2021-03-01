<template>
    <div class="question-box-container">
        <b-jumbotron>
              <template #lead>
                <span v-html="currentQuestion.question"></span>
              </template>

              <hr class="my-4">

              <b-list-group>
                <b-list-group-item 
                  v-for="(answer, index) in shuffledAnswers"
                  :key="index"
                  @click.prevent="selectAnswer(index)"
                  :class="answerClass(index)">
                  <span v-html="answer"></span>
                </b-list-group-item>
              </b-list-group>
          
              <b-button 
                variant="primary"
                @click="submitAnswer"
                :disabled="this.selectedIndex === null || this.answered || this.ended"
              >
                Submit
              </b-button>
              <b-button variant="success"
              @click="next" 
              :disabled="!submitted || end()"
              >
                Next
              </b-button>
              <b-button variant="danger"
              @click="reset" 
              >
                {{resetBtn}}
              </b-button>
        </b-jumbotron>
    </div>
</template>

<script>
import shuffle from 'lodash/shuffle'

export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function,
    end: Function,
    init: Function,
    numCorrect: Number,
    numTotal: Number
  },
  data: function(){
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      answered: false,
      submitted: false,
      ended: false,
      endClick: 0,
      resetBtn: "End Quiz",
    }
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler(){
        this.selectedIndex = null
        this.answered = false
        this.submitted = false
        this.shuffleAnswers()
      }
    }
  },
  methods: {
    selectAnswer(index){
      if(!this.submitted) {
        this.selectedIndex = index
      }
    },
    submitAnswer(){
      if (this.ended) {
        this.init()
        this.endClick = 0
      }
      else if(this.end()) {
        this.resetButton = "New Quiz"
        this.shuffledAnswers = []
        let score = this.calculateScore()
        this.currentQuestion.question = "Your score is " + score + "%!<br>" + this.rateScore(score)
        this.answered = false
        this.ended = true
        this.correctIndex = null
        this.selectedIndex = null
      }
      else
      {
        let isCorrect = false

        if(this.selectedIndex === this.correctIndex) {
          isCorrect = true
       }
        this.answered = true
        this.submitted = true

        this.increment(isCorrect)
      }
    },
    shuffleAnswers(){
      let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
      this.shuffledAnswers = shuffle(answers)
      this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
    },
    answerClass(index) {
      let answerClass = ''

      if(!this.answered && this.selectedIndex === index && !this.submitted) {
        answerClass = 'selected'
      } 
      else if (this.answered && this.correctIndex === index) {
        answerClass = 'correct'
      } 
      else if (this.answered && this.selectedIndex === index 
                  && this.correctIndex !== index) {
        answerClass = 'incorrect'
      } 
      return answerClass
    },
    calculateScore(){
      if (this.numTotal === 0) {
        return 0
      }
      else {
        return (this.numCorrect/this.numTotal*100).toFixed(2)
      }
    },
    rateScore(score){
      if(score < 50){
        return "You should probably read a book."
      }
      else if(score < 70){
        return "Don't quit your day job."
      }
      else if(score < 90){
        return "Not too bad."
      }
      else {
        return "Good job, Bill Gates!"
      }
    },
    reset(){
      if (this.endClick > 0) {
        this.init()
        this.endClick = 0
      }
      else{
        this.endClick++
        this.shuffledAnswers = []
        this.resetBtn = "New Quiz"
        let score = this.calculateScore()
        this.currentQuestion.question = "Your score is " + score + "%!<br>" + this.rateScore(score)
        this.answered = false
        this.ended = true
        this.correctIndex = null
        this.selectedIndex = null
      }
    }
  }
}
</script>

<style scoped>
  .list-group {
    margin-bottom: 15px
  }
  .list-group-item:hover {
    background: #EEE;
    cursor: pointer;
  }
  .btn {
    margin: 5px 5px
  }
  .selected {
    background-color: lightblue;
  }
  .correct {
    background-color: lightgreen;
  }
  .incorrect {
    background-color: pink;
  }
</style>