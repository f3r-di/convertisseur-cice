<template>
  <div class="c-jauges">
    <h1 class="c-jauges__title">Réinvestissez les fonds du CICE dans les secteurs publics.</h1>
    <div class="c-jauges__table">
      <div class="c-jauges__table__header">
        <h2 class="c-jauges__table__header__title">Métiers</h2>
        <div class="c--space"></div>
        <c-cice-jauge :joblist=joblist></c-cice-jauge>
      </div>
      <div class="c-jauges__jauge" v-for="(job, index) in joblist">
          <span class="c-jauges__jauge__title" :title="null">{{ job.plural }}</span>
          <div class="c--space"></div>
          <span class="c-jauges__jauge__jobs">{{ details[index] | bigNumber }} emplois</span>
          <input
            class="c-jauges__jauge__input"
            type="range"
            min="0"
            :max="totalCICE"
            :data-max="job.progress.max"
            :value="job.progress.value"
            @input="updateValue($event, index)">
      </div>
    </div>

    <c-job-result :joblist="joblist" ref="result"></c-job-result>
  </div>
</template>

<style lang="scss">
@import './theme';
@import './range';

.c-jauges {
  background: $flashyBlue;
  box-shadow: 0px 10px 20px 0px rgba($darkGrey, 0.2);
  padding: 100px 0;

  > .c-job-result {
    color: $white;
  }
}

.c-jauges__title {
  color: $white;
  font-size: 18px;
  font-weight: 500;
  margin: 0 0 100px 0;
  text-align: center;
}

.c-jauges__table {
  background: $white;
  border-radius: 8px;
  margin: 0 auto;
  width: 720px;
}

.c-jauges__table__header {
  align-items: center;
  border-bottom: 1px solid lighten($grey, 20%);
  display: flex;
  padding: 15px;
}

.c-jauges__table__header__title {
  font-size: 14px;
  margin: 0;
}

.c-jauges__jauge:not(:last-child) {
  border-bottom: 1px solid lighten($grey, 20%);
}

.c-jauges__jauge {
  display: flex;
  margin-top: -1px;
  padding: 10px;
}

.c-jauges__jauge__title {
  font-family: 'Montserrat', sans-serif;
  margin: 0;
}

.c-jauges__jauge__jobs {
  align-items: center;
  display: flex;
  font-family: 'Montserrat', sans-serif;
  font-size: 13px;
  margin: 0 5px;
}

.c-jauges__jauge__input {
  max-width: 300px;
}
</style>

<script>
import { totalCICE } from './joblist'

import CICEJauge from './CICEJauge.vue'
import CJobResult from './JobResult.vue'

export default {
  props: ['joblist'],

  components: {
    'c-cice-jauge': CICEJauge,
    CJobResult
  },

  data() {
    return {
      totalCICE,
      details: [0, 0, 0, 0, 0]
    }
  },

  methods: {
    update(joblist) {
      this.joblist = joblist

      this.details = this.joblist.map(job => {
        return Math.floor(job.progress.value / (job.costPerMonth * 1.3 * 12))
      })

      this.refreshProgressbars()
    },

    refreshProgressbars() {
      this.joblist.forEach((job, index) => {
        const jobMax = totalCICE - this.joblist
          .filter((_, j) => j !== index)
          .map(job => job.progress.value)
          .reduce((a, b) => a + b, 0)

        job.progress.max = jobMax

        return job
      })
    },

    updateValue($event, updatedIndex) {
      let value = parseInt($event.target.value, 10)
      value = Math.min(value, this.joblist[updatedIndex].progress.max)

      $event.target.value = value

      this.joblist[updatedIndex].progress.value = value

      this.refreshProgressbars()

      this.$emit('updateJobs')
    },

    showAllJobs() {
      this.$emit('showAllJobs')
    }
  }
}
</script>
