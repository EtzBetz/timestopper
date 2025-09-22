<template>
  <div class="timetable">
    <div class="timetable_overlay"></div>
    <div v-if="teamsData.length > 0" class="timetable_container">
      <TeamItem v-for="(teamData, index) in teamsData" :name="teamData.name" :time="teamData.time" :index="index"
                :key="index"
                @edit-team-name="handleNameChange"
                @delete-team="handleTeamDelete"
                class="timetable_team"></TeamItem>
    </div>
    <p v-else>Es sind noch keine Teams im Ziel angekommen.</p>
  </div>
</template>

<script>
import { Duration } from 'luxon'
import TeamItem from '@/components/TeamItem.vue'

export default {
  components: { TeamItem },
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
    },
    handleNameChange(teamIndex, newName) {
      this.$emit('edit-team-name', teamIndex, newName)
    },
    handleTeamDelete(teamIndex) {
      this.$emit('delete-team', teamIndex)
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
}
</style>