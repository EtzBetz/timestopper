<script>
import { DateTime } from 'luxon';
import TimetableComponent from './TimeTable.vue';

export default {
  components: {
    TimetableComponent
  },
  data() {
    return {
      startTime: null,
      elapsedTime: '',
      timetable: [],
      timer: null
    };
  },
  mounted() {
    this.startTime = DateTime.now();
    this.updateTimer();
    this.timer = setInterval(this.updateTimer, 1);
  },
  beforeUnmount() {
    clearInterval(this.timer);
  },
  methods: {
    updateTimer() {
      const now = DateTime.now();
      const diff = now.diff(this.startTime, ['hours', 'minutes', 'seconds', 'milliseconds']);

      const hours = Math.floor(diff.hours);
      const minutes = Math.floor(diff.minutes);
      const seconds = Math.floor(diff.seconds);
      const milliseconds = Math.floor(diff.milliseconds);

      this.elapsedTime = `${hours.toString().padStart(2, '0')}:${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}:${milliseconds.toString().padStart(3, '0')}`;
    },
    saveCurrentTime() {
      const now = DateTime.now();
      const diff = now.diff(this.startTime, ['hours', 'minutes', 'seconds', 'milliseconds']);

      const hours = Math.floor(diff.hours);
      const minutes = Math.floor(diff.minutes);
      const seconds = Math.floor(diff.seconds);
      const milliseconds = Math.floor(diff.milliseconds);

      this.timetable.push(`${hours.toString().padStart(2, '0')}:${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}:${milliseconds.toString().padStart(3, '0')}`);
    }
  }
};
</script>

<template>
  <div class="time-runner" @keydown="saveCurrentTime" @click="saveCurrentTime" tabindex="0">
    <p class="time-runner_title">Aktuelle Zeit:</p>
    <p class="time-runner_time">{{ elapsedTime }}</p>
    <TimetableComponent class="time-runner_table" :times="timetable" />

  </div>
</template>

<style scoped>
.time-runner {
  display: flex;
  flex-direction: column;
  align-items: center;
  user-select: none;
  gap: 1rem;
  margin: 10rem 0;
}

.time-runner_title {
  align-self: flex-start;
  font-size: 2rem;
  text-transform: uppercase;
  margin-left: 1rem;
  font-weight: bold;
}

.time-runner_time {
  font-size: 15rem;
  font-family: monospace;
  line-height: 0.85;
  color: #ff1b00;
}

.time-runner_table {
  margin-right: 1rem;
  position: relative;
}
</style>