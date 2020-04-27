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
          @click="selectAnswer(index)"
        >
          <div class="option" :class="{ selected: answer == index }">
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

    <div class="right">
      <Timer />
      <div class="Review">
        <h3>Question Answered</h3>
        <span>{{ answered() }} of {{ response.length }}</span>
        <div class="listOfNumber">
          <div
            class="numberQuestion"
            v-for="(answer, index) in response"
            :key="index"
            :class="{ answered: answer < 5 }"
            @click="changeNumber(index)"
          >
            {{ index + 1 }}
          </div>
        </div>
      </div>
      <button @click="postAnswer()">Submit</button>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Timer from "@/components/Timer.vue";

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

  components: {
    Timer
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

    setTimeout(() => {
      this.postAnswer();
    }, 100000);
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
      console.log(this.response[0]);
    },

    nextNumber: function() {
      if (this.number < this.questions.length) {
        this.number++;
        this.answer = this.response[this.number];
      }
    },

    backNumber: function() {
      if (this.number > 0) {
        this.number--;
        this.answer = this.response[this.number];
      }
    },

    changeNumber: function(number) {
      this.number = number;
    },

    async postAnswer() {
      this.scoring();
      axios
        .put("http://localhost:3004/User/2/", {
          score: this.score,
          answer: this.response
        })
        .then(resp => {
          console.log(resp.data);
        })
        .catch(error => {
          console.log(error);
        });
      //this.$router.push('Home');
    },

    answered: function() {
      var total = 0;
      for (let i=0; i<this.questions.length; i++) {
        if (this.response[i] < 5) {
          total++;
        }
      }
      return total
    },

    scoring() {
      for (let i = 0; i < this.response.length; i++) {
        if (
          typeof this.questions[i].choices[this.response[i]] !== "undefined" &&
          this.questions[i].choices[this.response[i]].correct
        ) {
          this.score++;
        }
      }
      console.log(this.score);
    }
  }
};
</script>

<style scoped lang="scss">
$orange: #e5792e;
$lightOrange: #f9bc93;
$grey: #eaebed;
$space: 1rem;

.wrapper{
  display: flex;
  flex-wrap: wrap ;
  justify-content: space-between;
  padding: 3%;
  background-color: #F5F7F9;
}

.question {
  width: 60%;
  background-color: white;
  padding: 1%;
  border-radius: 1rem;
  min-width: 15rem;
  margin-bottom: $space;
}

.questionBox {
  border: 2px solid $grey;
  border-radius: 1rem;
  margin: $space;
  padding: $space;
  min-width: 11rem;
}

.answerBox {
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
  min-width: 15rem;
  margin-bottom: 1rem;
}

.number {
  margin: 5px 0;
}

button {
  width: 40%;
  margin: auto;
  padding: 1rem;
  border-radius: 2rem;
  border: 2px solid $orange;
  background: $orange;
  color:white;

  &:active {
    transform: scaleX(0.9);
    transition-duration: 0.3s;
  }

  &:hover {
    border: 2px solid $lightOrange;
    background-color: $lightOrange;
    transition-duration: 0.3s;
  }
}

.right {
  width: 30%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  min-width: 15rem;
}

.Review {
  padding: 1rem;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  border-radius: $space;
  margin-bottom: 30px;
  background-color: white;
  text-align: center;
}

h3 {
  color:$orange;
  margin: 0 0 1rem 0;
}

.listOfNumber {
  display: flex;
  flex-wrap:wrap;
  justify-content: center;
}

.numberQuestion {
  height: 2.5rem;
  width: 2.5rem;
  border-radius: 2rem;
  border: $lightOrange 2px solid;
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 1rem 1rem 1rem 0rem;
  cursor: pointer;
}

.answered {
  border: transparent 2px solid;
  background-color: $orange;
  color: white;
}
</style>
