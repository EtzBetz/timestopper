<script>
import { Duration } from 'luxon'

export default {
  components: {},
  props: {
    index: Number,
    name: {
      type: String,
      required: false,
      default: ''
    },
    time: {
      type: String,
      required: true,
      default: ''
    }
  },

  data() {
    return {
      newName: ''
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
    editTeamName() {
      this.$emit('edit-team-name', this.index, this.newName)
      this.newName = ''
    },
    deleteTeam() {
      this.$emit('delete-team', this.index)
    }
  }
}
</script>

<template>
  <div class="team">
    <div class="team_edit">
      <button @click="deleteTeam()">delete team</button>
      <input type="text" v-model="this.newName" placeholder="new name" />
      <button @click="editTeamName()">set name</button>
    </div>
    {{ name != null ? name : 'Team ' + (index + 1) }}:&nbsp;&nbsp;<span
    class="team_time">{{ getDiffTimeString(time) }} [{{ index + 1 }}]</span>
  </div>
</template>

<style scoped lang="scss">
.team {
  display: flex;
  flex-direction: row;
  justify-content: end;

  &:hover > .team_edit {
    opacity: 1;
  }
}

.team_time {
  font-family: monospace;
}

.team_edit {
  position: fixed;
  display: flex;
  flex-direction: row;
  padding: 10px;
  background-color: #f2f2f2;
  border-radius: 10px;
  box-shadow: 0 4px 4px 0 rgba(0, 0, 0, 0.1);
  opacity: 0;
  transform: translateY(50%);
  transition: opacity 0.2s ease-in-out;
}
</style>