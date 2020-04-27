<template>
<transition name="modal-fade">
  <div class="modal-backdrop">
    <div class="modal"
      role="dialog"
      aria-labelledby="modalTitle"
      aria-describedby="modalDescription"
    >
      <header
        class="modal-header"
        id="modalTitle"
      >
        <slot name="header">
          Review

          <button
            type="button"
            class="btn-close"
            @click="close"
            aria-label="Close modal"
          >
            x
          </button>
        </slot>
      </header>
      <section
        class="modal-body"
        id="modalDescription"
      >
        <slot name="body">
          I'm the default body!
        </slot>
      </section>
      <footer class="modal-footer">
        <slot name="footer">
          I'm the default footer!

          <button
            type="button"
            class="btn-green"
            @click="close"
            aria-label="Close modal"
          >
            Close me!
          </button>
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
    score: Number
  },
  methods: {
    close() {
      this.$emit('close');
    }
  }
}
</script>
