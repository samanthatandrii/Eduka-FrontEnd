<template>
  <div class="wrapper">
    <div class="wrapperTest" v-show="!finished">
      <div class="question">
        <div class="pagination start">
          <h2>Test 1</h2>
          <div class="pagination end">
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

        <div class="pagination end">
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
              :class="{ answered: answer < 5, now: number == index }"
              @click="changeNumber(index)"
            >
              {{ index + 1 }}
            </div>
          </div>
        </div>
        <button @click="showModal()">Review</button>
        <button @click="postAnswer()">Submit</button>
      </div>
      <Modal
        :result="response"
        :answered="answered()"
        v-show="isModalVisible"
        @close="closeModal"
      />
    </div>

    <div class="wrapperResult" v-show="finished">
      <div class="result">
        <h2>Result</h2>
        <h3 class="resultScore">
          Score {{ score }} out of {{ response.length }}
        </h3>
        <div class="listOfAnswer">
          <ol>
            <li v-for="(answer, index) in response" :key="index">
              <h4>
                You answered
                <span class="lightOrange">{{ answer | capitalize }}</span>
              </h4>
              <p class="lightOrange">The correct answer is:</p>
              <p class="thin">{{ returnCorrectAnswer(index) }}</p>
            </li>
          </ol>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Timer from "@/components/Timer.vue";
import Modal from "@/components/Modal.vue";

export default {
  name: "Question",
  data() {
    return {
      questions: [],
      number: 0,
      response: [],
      answer: 5,
      score: 0,
      isModalVisible: false,
      finished: false
    };
  },

  components: {
    Timer,
    Modal
  },

  filters: {
    charIndex: function(i) {
      var r = "-";
      if (i == 5) {
        return r;
      }
      return String.fromCharCode(97 + i);
    },

    capitalize: function(i) {
      var r = "-";
      if (i == 5) {
        return r;
      }
      return String.fromCharCode(97 + i).toUpperCase();
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
    }, 600000);
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
      this.answer = this.response[this.number];
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
      this.showResult();
    },

    answered: function() {
      var total = 0;
      for (let i = 0; i < this.questions.length; i++) {
        if (this.response[i] < 5) {
          total++;
        }
      }
      return total;
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
    },

    returnCorrectAnswer(num) {
      for (let i = 0; i < this.questions[num].choices.length; i++) {
        if (
          typeof this.questions[num].choices[i] !== "undefined" &&
          this.questions[num].choices[i].correct
        ) {
          return this.questions[num].choices[i].choice;
        }
      }
      return null;
    },

    showModal() {
      this.isModalVisible = true;
    },

    closeModal() {
      this.isModalVisible = false;
    },

    showResult() {
      this.isModalVisible = false;
      this.finished = true;
    }
  }
};
</script>

<style scoped lang="scss">
$orange: #e5792e;
$lightOrange: #f9bc93;
$grey: #eaebed;
$blue: #7ed8ec;
$space: 1rem;

.wrapperResult {
  padding: 3%;
  background-color: #f5f7f9;
  display: flex;
  justify-content: center;
}

.result {
  width: 95%;
  background-color: white;
  padding: $space;
  border-radius: 1rem;
  min-width: 15rem;
  margin-top: 5rem;
  text-align: center;
}

.listOfAnswer {
  margin-top: 2rem;
  margin-left: 1rem;
  font-weight: bold;
  text-align: left !important;

  li {
    margin-bottom: 1rem;
  }
}

.lightOrange {
  color: $orange;
  font-weight: bold;
  display: inline;
}

.thin {
  font-weight: 300;
}

.resultScore {
  margin: 1rem 0rem;
}

.wrapperTest {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  padding: 3%;
  background-color: #f5f7f9;
}

.question {
  width: 60%;
  background-color: white;
  padding: 1%;
  border-radius: 1rem;
  min-width: 15rem;
  margin-top: 5rem;
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
  min-width: 15rem;
  margin-bottom: 1rem;

  &.start {
    justify-content: space-between;
    align-items: baseline;

    h2 {
      margin-left: $space;
    }
  }

  &.end {
    justify-content: flex-end;
  }
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
  color: white;
  margin-bottom: 1rem;

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
  margin-top: 5rem;
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
  color: $orange;
  margin: 0 0 1rem 0;
}

.listOfNumber {
  display: flex;
  flex-wrap: wrap;
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
  background-color: $orange !important;
  color: white;
}

.now {
  background-color: $lightOrange;
}

$breakpoint-tablet: 660px;
@media (max-width: $breakpoint-tablet) {
  .question {
    width: 100%;
  }

  .right {
    width: 100%;
    margin-top: 1rem;
  }
}
</style>
