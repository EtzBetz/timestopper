<template>
  <div class="timetable">
    <div class="timetable_overlay"></div>
    <div v-if="times.length > 0" class="timetable_container">
      <p v-for="(time, index) in times" :key="index" class="timetable_team">
        Team {{ index + 1 }}:&nbsp;&nbsp;<span class="timetable_team_time">{{ time }}</span>
      </p>
    </div>
    <p v-else>Es sind noch keine Teams ins Ziel gekommen.</p>
  </div>
</template>

<script>
export default {
  props: {
    times: {
      type: Array,
      required: true,
      default: () => []
    }
  }
};
</script>

<style scoped lang="scss">
.timetable {
  align-self: flex-end;
  margin-top: 1rem;
  font-size: 5rem;
  color: #333;
  max-height: 58vh;
  overflow-y: scroll;

  scrollbar-width: none;

  .timetable_overlay{
    position: fixed;
    pointer-events: none;
    //position: relative;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: linear-gradient(to bottom, rgba(255, 255, 255, 0.0) 50%, rgba(255, 255, 255, 1) 95%);
    z-index: 0;
  }

  .timetable_container{
    display: flex;
    flex-direction: column-reverse;
  }

  .timetable_team {
    text-align: end;
    border-radius: 4px;
    letter-spacing: -0.25rem;
  }
  .timetable_team_time {
    font-family: monospace;
  }
}
</style>