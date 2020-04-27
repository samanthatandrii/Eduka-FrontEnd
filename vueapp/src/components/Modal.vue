<template>
  <transition name="modal-fade">
    <div class="modalBackdrop">
      <div
        class="modal"
        role="dialog"
        aria-labelledby="modalTitle"
        aria-describedby="modalDescription"
      >
        <header class="modalHeader" id="modalTitle">
          <slot name="header">
            <h2>Review</h2>

            <button
              type="button"
              class="btnClose"
              @click="close"
              aria-label="Close modal"
            >
              X
            </button>
          </slot>
        </header>
        <section class="modalBody" id="modalDescription">
          <slot name="body" class="reviewList">
            <div class="review" v-for="(choice, index) in result" :key="index">
              <span class="number">{{ index + 1 }}</span>
              <span class="choice">{{ choice | charIndex }}</span>
            </div>
          </slot>
        </section>
        <footer class="modalFooter">
          <slot name="footer">
            <h3>
              Question Answered <span class="blue">{{ answered }}</span> of
              {{ result.length }}
            </h3>
          </slot>
        </footer>
      </div>
    </div>
  </transition>
</template>

<script>
export default {
  name: "Modal",
  props: {
    result: Array,
    answered: Number
  },

  filters: {
    charIndex: function(i) {
      var r = "-";
      if (i == 5) {
        return r;
      }
      return String.fromCharCode(97 + i).toUpperCase();
    }
  },

  methods: {
    close() {
      this.$emit("close");
    }
  }
};
</script>

<style lang="scss" scoped>
$orange: #e5792e;
$lightOrange: #f9bc93;
$grey: #eaebed;

.modalBackdrop {
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  background-color: rgba(0, 0, 0, 0.3);
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal {
  background: white;
  border-radius: 2rem;
  overflow-x: auto;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
  width: 30%;
  min-width: 15rem;
  padding: 2rem;
  max-height: 100vh;
}

.modalHeader {
  display: flex;
  justify-content: space-between;
  align-items: baseline;
}

button {
  border: none;
  font-size: 25px;
  cursor: pointer;
  color: grey;
  background: transparent;
}

h2 {
  color: $orange;
}

.modalBody {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-evenly;
}

.review {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  margin-right: 1.5rem;
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
  cursor: pointer;
}

.choice {
  font-weight: bold;
}

.blue {
  color: #7ed8ec;
  font-weight: bold;
}
</style>
