<template>
  <div class="question">
    <div class="next"></div>
    <div class="number">{{ number }}</div>
    <div class="next"></div>
    <div class="questionBox box">
      <h4>{{ questions[number].question }}</h4>
    </div>
    <div class="answerBox box">
      <div
        class="option"
        v-for="(choice, index) in questions[number].choices"
        @click="selectAnswer(index)"
        :class="{ 'is-selected': response[number] == index }"
        :key="index"
      >
        {{ index | charIndex }}. {{ choice.choice }}
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "Question",
  data() {
    return {
      questions: [],
      number: 0,
      response: []
    };
  },

  filters: {
    charIndex: function(i) {
      return String.fromCharCode(97 + i);
    }
  },

  async created() {
    try {
      const res = await axios.get(`http://localhost:3000/questions`);
      this.questions = res.data;
      this.number = 0;
      this.response = Array(this.questions.length).fill(null);
    } catch (e) {
      console.error(e);
    }
  },

  methods: {
    restart: function() {
      this.number = 0;
      this.response = Array(this.questions.length).fill(false);
      console.log(this.response);
    },

    selectAnswer: function(choice) {
      console.log(choice);
      this.response[this.number] = choice;
    },

    nextNumber: function() {
      if (this.number < this.questions.length) {
        this.number++;
      }
    },

    backNumber: function() {
      if (this.number < 0) {
        this.number--;
      }
    }
  }
};
</script>

<style scoped lang="scss">
$orange: #e5792e;
$grey: #f5f7f9;
$space: 1rem;
// $breakpoints: (
//   phone: 640px,
//   tablet: 768px,
//   desktop: 1024px
// ) !default;

// @include media(">phone", "<tablet") {

// }

// @include media(">tablet", "<950px") {

// }

.box {
  border: 2px solid $grey;
  border-radius: 1rem;
  margin: $space;
  padding: $space;
  width: 30rem;
}

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
