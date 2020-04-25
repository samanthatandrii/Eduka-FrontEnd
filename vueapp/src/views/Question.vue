<template>
  <div class="wrapper">
    <div class="question">
      <div class="pagination">
        <div class="option" @click="backNumber()" :disabled="number < 0">
          <font-awesome-icon :icon="['fas', 'chevron-left']" />
        </div>
        <div class="number">{{ number + 1 }}</div>
        <div
          class="option"
          @click="nextNumber()"
          :disabled="number > questions.length"
        >
          <font-awesome-icon :icon="['fas', 'chevron-right']" />
        </div>
      </div>

      <div class="questionBox">
        <h4>{{ questions[number].question }}</h4>
      </div>
      <div class="answerBox">
        <div
          class="options"
          v-for="(choice, index) in questions[number].choices"
          :key="index"
        >
          <div
            class="option"
            @click="selectAnswer(index)"
            :class="{ selected: answer == index }"
          >
            {{ index | charIndex }}
          </div>
          <span>{{ choice.choice }}</span>
        </div>
      </div>

      <div class="pagination">
        <div class="option" @click="backNumber()" :disabled="number < 0">
          <font-awesome-icon :icon="['fas', 'chevron-left']" />
        </div>
        <div class="number">{{ number + 1 }}</div>
        <div
          class="option"
          @click="nextNumber()"
          :disabled="number > questions.length"
        >
          <font-awesome-icon :icon="['fas', 'chevron-right']" />
        </div>
      </div>
    </div>

    <button @click="submitAnswer()">Submit</button>
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
      response: [],
      answer: 5,
      score: 0
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
      this.response = Array(this.questions.length).fill(5);
    } catch (e) {
      console.error(e);
    }
  },

  methods: {
    restart: function() {
      this.number = 0;
      this.response = Array(this.questions.length).fill(5);
      console.log(this.response);
    },

    selectAnswer: function(choice) {
      this.answer = choice;
      this.response[this.number] = choice;
      console.log(this.answer == choice);
    },

    nextNumber: function() {
      if (this.number < this.questions.length) {
        this.number++;
      }
    },

    backNumber: function() {
      if (this.number > 0) {
        this.number--;
      }
    },

    postAnswer: function() {
      axios
        .post("http://localhost:3004/User", {
          id: 1,
          score: this.score,
          answer: this.response
        })
        .then(resp => {
          console.log(resp.data);
        })
        .catch(error => {
          console.log(error);
        });
    }
  }
};
</script>

<style scoped lang="scss">
$orange: #e5792e;
$lightOrange: #f9bc93;
$grey: #f5f7f9;
$space: 1rem;

.questionBox {
  border: 2px solid $grey;
  border-radius: 1rem;
  margin: $space;
  padding: $space;
  width: 40%;
  min-width: 15rem;
}

.answerBox {
  width: 40%;
  min-width: 15rem;
}

.options {
  display: flex;
  flex-direction: row;
  align-items: center;
}

.option {
  border: 2px solid $grey;
  border-radius: 2rem;
  width: 30px;
  height: 30px;
  display: flex;
  justify-content: center;
  align-items: center;
  margin: $space;
  cursor: pointer;

  &.selected {
    border: 2px solid transparent;
    background-color: $orange;
    color: white;
  }

  &:hover {
    border: 2px solid transparent;
    background-color: $lightOrange;
    color: white;
  }
}

.pagination {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: flex-end;
  width: 40%;
  min-width: 15rem;
  margin-bottom: 1rem;
  left: 4rem;
  position: relative;
}

.number {
  margin: 5px 0;
}
</style>
