<template>
  <div class="countdown">
    <h3>
      {{ timeLeft }}
    </h3>
    <p>
      Tryout akan selesai pada <span>{{ endTime }}</span>
    </p>
  </div>
</template>

<script>
var intervalTimer;

export default {
  name: "Timer",
  data() {
    return {
      timeLeft: "00:00",
      endTime: "0",
      time: 100
    };
  },

  created() {
    clearInterval(intervalTimer);
    this.timer(this.time);
  },

  methods: {
    timer(seconds) {
      const now = Date.now();
      const end = now + seconds * 1000;
      this.displayTimeLeft(seconds);

      this.selectedTime = seconds;
      this.displayEndTime(end);
      this.countdown(end);
    },
    countdown(end) {
      intervalTimer = setInterval(() => {
        const secondsLeft = Math.round((end - Date.now()) / 1000);

        if (secondsLeft === 0) {
          this.endTime = 0;
        }

        if (secondsLeft < 0) {
          clearInterval(intervalTimer);
          return;
        }
        this.displayTimeLeft(secondsLeft);
      }, 1000);
    },
    displayTimeLeft(secondsLeft) {
      const minutes = Math.floor((secondsLeft % 3600) / 60);
      const seconds = secondsLeft % 60;

      this.timeLeft = `${zeroPadded(minutes)}:${zeroPadded(seconds)}`;
    },
    displayEndTime(timestamp) {
      const end = new Date(timestamp);
      const hour = end.getHours();
      const minutes = end.getMinutes();

      this.endTime = `${hourConvert(hour)}:${zeroPadded(minutes)}`;
    }
  }
};

function zeroPadded(num) {
  // 4 --> 04
  return num < 10 ? `0${num}` : num;
}

function hourConvert(hour) {
  // 15 --> 3
  return hour % 12 || 12;
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
$orange: #e5792e;
$lightOrange: #f9bc93;
$grey: #eaebed;
$space: 1rem;

.countdown {
  margin: 5px 0 30px;
  border-radius: $space;
  background-color: white;
  padding: 4%;
  border-radius: 1rem;
}
h3 {
  font-size: 40px;
  display: flex;
  align-items: baseline;
  justify-content: center;
  margin: 0px auto;
  color: $orange;
}
p {
  display: flex;
  align-items: baseline;
  justify-content: center;
  margin: 0px 2rem 15px 2rem;
}
p span {
  width: 70px;
  border-bottom: 2px solid $lightOrange;
  text-align: center;
  margin-left: 5%;
}
.time {
  display: flex;
  justify-content: center;
}
.columns {
  margin-left: 0;
  margin-right: 0;
}
</style>
