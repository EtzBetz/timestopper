<template>
  <div class="timetable">
    <div class="timetable_overlay"></div>
    <div v-if="teamsData.length > 0" class="timetable_container">
      <p v-for="(teamData, index) in teamsData" :key="index" class="timetable_team">
        <button @click="editTeamName(index)">set name</button>
        {{ teamData.name != null ? teamData.name : 'Team ' + (index + 1) }}:&nbsp;&nbsp;<span
        class="timetable_team_time">{{ getDiffTimeString(teamData.time) }} [{{ index + 1 }}]</span>
      </p>
    </div>
    <p v-else>Es sind noch keine Teams im Ziel angekommen.</p>
  </div>
</template>

<script>
import { Duration } from 'luxon'

export default {
  props: {
    teamsData: {
      type: Array,
      required: true,
      default: () => []
    },
    teamName: {
      type: String,
      required: true,
      default: ''
    }
  },
  methods: {
    getDiffTimeString(diff) {
      const teamTime = Duration.fromISO(diff)

      const hours = Math.floor(teamTime.hours)
      const minutes = Math.floor(teamTime.minutes)
      const seconds = Math.floor(teamTime.seconds)
      const milliseconds = Math.floor(teamTime.milliseconds)

      return `${hours.toString().padStart(2, '0')}:${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}:${milliseconds.toString().padStart(3, '0')}`
    },
    editTeamName(teamIndex) {
      this.$emit('edit-team-name', teamIndex)

      // let teamsDataStr = localStorage.getItem('teams-data')
      // const teamsData = JSON.parse(teamsDataStr)
      // teamsData[teamIndex].name = this.teamName
      // teamsDataStr = JSON.stringify(teamsData)
      // localStorage.setItem('teams-data', teamsDataStr)
    }
  }
}
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

  .timetable_overlay {
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

  .timetable_container {
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