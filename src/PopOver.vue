<template>
  <div class="c-popover" v-html="jobs" :style="left" ref="popover"></div>
</template>

<style lang="scss">
@import './theme';

.c-popover {
  background: $white;
  border: 1px solid rgba($grey, 0.3);
  bottom: -30px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, .3);
  font-family: 'Montserrat', sans-serif;
  font-size: 12px;
  left: 0;
  height: 24px;
  padding: 5px 10px;
  position: absolute;
  transform: translateX(calc(-50% + 11.5px));
}
</style>

<script>
export default {
  props: ['job'],

  data() {
    return {
      maxPx: 0
    }
  },

  computed: {
    jobs() {
      const jobs = Math.floor(this.job.progress.value / (this.job.costPerMonth * 1.3 * 12))

      return this.$options.filters.bigNumber(jobs) + ' emplois'
    },

    left() {
      return { left: `${this.job.progress.value / this.job.progress.max * 100}%` }
    }
  }
}
</script>
