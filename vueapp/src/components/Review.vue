<template>
  <div class="Review">
    <h3>Question Answered</h3>
    <span>{{ answered.length }} of {{ results.length }}</span>
    <div class="listOfNumber">
      <div
        class="number"
        v-for="(answer, index) in answered"
        :key="index"
        :class="{ answered: answer < 5 }"
      >
        {{ index + 1 }}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Review",
  props: {
    results: Array
  },
  data() {
    return {
      answered: this.results
    };
  },
  beforeUpdate() {
    this.answered = this.results;
  },
  
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
$orange: #e5792e;
$lightOrange: #f9bc93;
$grey: #e8e8e9;
$space: 1rem;

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

.number {
  height: 2.5rem;
  width: 2.5rem;
  border-radius: 2rem;
  border: $lightOrange 2px solid;
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 1rem 1rem 1rem 0rem;
}

.answered {
  border: transparent 2px solid;
  background-color: $orange;
  color: white;
}
</style>
