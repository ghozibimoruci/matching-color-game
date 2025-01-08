<template>
  <div>
    {{ formattedTime }}
  </div>
</template>

<script>
export default {
  name: 'Timer',
  props: {
    initialTime: {
      type: Number,
      required: true
    }
  },
  data() {
    return {
      time: this.initialTime,
      timer: null
    }
  },
  computed: {
    formattedTime() {
      const minutes = Math.floor(this.time / 60).toString().padStart(2, '0');
      const seconds = (this.time % 60).toString().padStart(2, '0');

      return `${minutes}:${seconds}`;
    }
  },
  mounted() {
    this.timer = setInterval(() => {
      this.time--;
      if (this.time <= 0) {
        clearInterval(this.timer);
        this.$emit('timer-end');
      }
    }, 1000);
  },
  beforeUnmount() {
    clearInterval(this.timer);
  }
}
</script>
