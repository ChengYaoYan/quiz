<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template slot="lead">
        <p>{{ question.question }}</p>
      </template>

      <hr class="my-4" />

      <b-list-group class="list-group">
        <b-list-group-item
          class="list-group-item"
          v-for="(answer, index) in answers"
          :key="index"
          :class="answerClass(index)"
          :disabled="answered"
          @click="selectAnswer(index)"
        >
          {{ answer }}
        </b-list-group-item>
      </b-list-group>

      <b-button
        variant="primary"
        @click="submitAnswer"
        :disabled="selectedIndex == null || answered"
        >Submit</b-button
      >
      <b-button @click="next" variant="success">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from "lodash";

export default {
  props: {
    question: Object,
    next: Function,
    increment: Function,
  },
  data() {
    return {
      selectedIndex: null,
      correctIndex: 1,
      shuffledAnswers: [],
      answered: false,
    };
  },
  watch: {
    question: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.answered = false;
        this.shuffleAnswers();
      },
    },
  },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index;
    },
    shuffleAnswers() {
      let answers = [
        ...this.question.incorrect_answers,
        this.question.correct_answer,
      ];
      this.shuffledAnswers = _.shuffle(answers);
      this.correctIndex = this.shuffledAnswers.indexOf(
        this.question.correct_answer
      );
    },
    submitAnswer() {
      let isCorrect = false;

      if (this.selectedIndex == this.correctIndex) {
        isCorrect = true;
      }

      this.answered = true;
      this.increment(isCorrect);
    },
    answerClass(index) {
      let answerClass = "";

      if (!this.answered && index == this.selectedIndex) {
        answerClass = "selected";
      } else if (this.answered && index == this.correctIndex) {
        answerClass = "correct";
      } else if (
        this.answered &&
        index == this.selectedIndex &&
        index != this.correctIndex
      ) {
        answerClass = "incorrect";
      }

      return answerClass;
    },
  },
  computed: {
    answers() {
      let answers = [...this.question.incorrect_answers];
      answers.push(this.question.correct_answer);
      return answers;
    },
  },
};
</script>

<style scoped>
.list-group {
  margin-bottom: 1rem;
}
.list-group-item:hover {
  background-color: #eee;
  cursor: pointer;
}
.selected {
  background-color: lightblue;
}
.correct {
  background-color: lightgreen;
}
.incorrect {
  background-color: red;
}
</style>