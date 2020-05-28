<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template v-slot:lead>
        {{ currentQuestion.question }}
      </template>

      <hr class="my-4">

      <b-list-group>
        <b-list-group-item 
          v-for="(answer, index) in answers" :key="index" 
          v-on:click="selectAnswer(index)"
          v-bind:class="answerClass(index)"
        >
          {{ answer }}
        </b-list-group-item>
      </b-list-group>

      <!-- <p v-for="(answer, index) in answers" :key="index">
        {{ answer }}
      </p> -->

      <b-button 
        variant="primary" 
        v-on:click="submitAnswer"
        v-bind:disabled="selectedIndex === null || answered"
      >
        Submit
      </b-button>
      <b-button 
        v-on:click="next" 
        variant="success"
        v-bind:disabled="isLastQuestion"
      >
        Next
      </b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from 'lodash';

export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function,
    isLastQuestion: Boolean
  },
  data() {
    return {
      selectedIndex: null,
      correctIndex: null,
      // shuffledAnswers: []      
      answered: false
    }
  },
  computed: {
    answers() {
      let answers = [...this.currentQuestion.incorrect_answers]
      answers.push(this.currentQuestion.correct_answer)
      answers = _.shuffle(answers)
      // this.correctIndex = this.answers.indexOf(this.currentQuestion.correct_answer)
      console.log(answers)
      return answers
    }    
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.correctIndex = this.answers.indexOf(this.currentQuestion.correct_answer);
        console.log(this.currentQuestion.correct_answer)
        console.log(this.correctIndex)
        // this.shuffleAnswers();
        this.answered = false
      }
    }
  },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index;
    },
    // shuffleAnswers() {
    //   let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
    //   this.shuffledAnswers = _.shuffle(answers)
    // },
    submitAnswer() {
      let isCorrect = false

      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true
      }
      this.answered = true

      this.increment(isCorrect)
    },
    answerClass(index) {
      let answerClass = ''

      if (!this.answered && this.selectedIndex === index) {
        answerClass = 'selected'
      } else if (this.answered && this.correctIndex === index) {
        answerClass = 'correct disabled'
      } else if (this.answered && this.correctIndex !== this.selectedIndex && index === this.selectedIndex) {
        answerClass = 'incorrect disabled'
      } else if (this.answered && index !== this.selectedIndex) {
        answerClass = 'disabled'
      } else {
        answerClass = ''
      }

      // [
      //   !answered && selectedIndex === index ? 'selected' : 
      //   answered && correctIndex === index ? 'correct disabled' :
      //   answered && correctIndex !== selectedIndex && index === selectedIndex ? 'incorrect disabled' : 
      //   answered && index !== selectedIndex ? 'disabled' : ''        
      // ]

      return answerClass

    }
  }
}
</script>

<style scoped>
  .list-group {
    margin-bottom: 15px;
  }

  .list-group-item:hover {
    background: #DDD;
    cursor: pointer;
  }

  .btn {
    margin: 0 5px;
  }

  .selected {
    background-color: lightblue !important;
  }

  .correct {
    background-color: lightgreen;
  }

  .incorrect {
    background-color: red;
  }
</style>