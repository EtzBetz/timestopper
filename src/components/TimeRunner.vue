<script>
import TimetableComponent from './TimeTable.vue'
import { DateTime, Duration } from 'luxon'

export default {
  components: {
    TimetableComponent
  },
  data() {
    return {
      startTime: null,
      elapsedTime: '',
      timetable: [],
      timer: null,
      timeInput: '',
      teamNrInput: 1,
      teamNameInput: '',
      showTopTeams: false
    }
  },
  mounted() {
    this.loadStartTime()
    this.loadTeamsData()
    this.updateTimer()
    this.timer = setInterval(this.updateTimer, 1)
  },
  beforeUnmount() {
    clearInterval(this.timer)
  },
  methods: {
    Duration() {
      return Duration
    },
    updateTimer() {
      const now = DateTime.now()
      const diff = now.diff(this.startTime, ['hours', 'minutes', 'seconds', 'milliseconds'])
      this.elapsedTime = this.getDiffTimeString(diff)
    },
    addTeamTime() {
      const now = DateTime.now()
      const teamTime = now.diff(this.startTime, ['hours', 'minutes', 'seconds', 'milliseconds'])

      const teamData = {
        name: null,
        time: teamTime.toISO()
      }

      let teamsDataStr = localStorage.getItem('teams-data')
      let teamsData
      if (teamsDataStr == null) {
        teamsData = [teamData]
        teamsDataStr = JSON.stringify(teamsData)
      } else {
        teamsData = JSON.parse(teamsDataStr)
        teamsData.push(teamData)
        teamsDataStr = JSON.stringify(teamsData)
      }
      localStorage.setItem('teams-data', teamsDataStr)
      this.timetable = teamsData
    },
    getDiffTimeString(diff) {
      const hours = Math.floor(diff.hours)
      const minutes = Math.floor(diff.minutes)
      const seconds = Math.floor(diff.seconds)
      const milliseconds = Math.floor(diff.milliseconds)

      return `${hours.toString().padStart(2, '0')}:${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}:${milliseconds.toString().padStart(3, '0')}`
    },
    deleteTeamTime() {
      let teamsDataStr = localStorage.getItem('teams-data')
      let teamsData = JSON.parse(teamsDataStr)
      teamsData.splice(this.teamNrInput - 1, 1)
      teamsDataStr = JSON.stringify(teamsData)
      localStorage.setItem('teams-data', teamsDataStr)
      this.timetable = teamsData
    },
    loadStartTime() {
      const storedTime = localStorage.getItem('start-time')
      if (storedTime == null) {
        this.resetStartTime()
      } else {
        this.startTime = DateTime.fromISO(storedTime)
      }
    },
    loadTeamsData() {
      const storedTeamsData = localStorage.getItem('teams-data')
      if (storedTeamsData == null) {
        this.resetTeamsData()
      } else {
        this.timetable = JSON.parse(storedTeamsData)
      }
    },
    saveStartTime() {
      localStorage.setItem('start-time', this.startTime.toISO())
    },
    saveTeamsData() {
      const teamsDataStr = JSON.stringify(this.timetable)
      localStorage.setItem('teams-data', teamsDataStr)
    },
    resetStartTime() {
      this.startTime = DateTime.now()
      this.saveStartTime()
    },
    resetTeamsData() {
      this.timetable = []
      this.saveTeamsData()
    },
    setManualStartTime() {
      this.startTime = DateTime.fromISO(this.timeInput)
    },
    toggleShowTopTeams() {
      this.showTopTeams = !this.showTopTeams
    },
    handleNameChange(teamIndex) {
      let teamsDataStr = localStorage.getItem('teams-data')
      let teamsData = JSON.parse(teamsDataStr)
      teamsData[teamIndex].name = this.teamNameInput
      teamsDataStr = JSON.stringify(teamsData)
      localStorage.setItem('teams-data', teamsDataStr)
      this.timetable = teamsData
      this.teamNameInput = ''
    }
  }
}
</script>

<template>
  <div class="time-runner">
    <div class="time-runner_controls">
      <div class="time-runner_controls_row">
        start time: <span class="time-runner_controls_start-time">{{ startTime }}</span>
        <button @click="resetStartTime">reset starttime</button>
        <span><input type="text" placeholder="yyyy-mm-ddThh:mm:ss.mmm+02:00" size="30" v-model="timeInput">
          <button @click="setManualStartTime">set start time</button></span>
      </div>
      <div class="time-runner_controls_row">
        <span><input type="number" placeholder="team-nr" v-model="teamNrInput" size="1" min="1" max="1000">
          <button @click="deleteTeamTime">delete team time</button></span>
        <button @click="resetTeamsData">reset ALL times</button>
        <input type="text" placeholder="team-name" v-model="teamNameInput">
        <button @click="toggleShowTopTeams">toggle top teams</button>
      </div>
    </div>
    <p class="time-runner_title" tabindex="1">Aktuelle Zeit:</p>
    <p class="time-runner_time" @keydown="addTeamTime" tabindex="0">{{ elapsedTime }}</p>
    <div v-if="showTopTeams" class="time-runner_top-teams">
      <span
        class="top-team--1">{{ timetable[0]?.name ?? 'Team 1'
        }}<br>{{ getDiffTimeString(Duration().fromISO(timetable[0].time))
        }}</span>
      <span class="top-team--2">{{ timetable[1]?.name ?? 'Team 2'
        }}<br>{{ getDiffTimeString(Duration().fromISO(timetable[1].time))
        }}</span>
      <span class="top-team--3">{{ timetable[2]?.name ?? 'Team 3'
        }}<br>{{ getDiffTimeString(Duration().fromISO(timetable[2].time))
        }}</span>
    </div>
    <TimetableComponent v-if="!showTopTeams" class="time-runner_table" :teamsData="timetable"
                        :teamName="teamNameInput" @edit-team-name="handleNameChange" />
  </div>
</template>

<style scoped lang="scss">
.time-runner {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-between;
  user-select: none;
  gap: 1rem;
  //margin: 10rem 0;
}

.time-runner_title {
  align-self: flex-start;
  font-size: 2rem;
  text-transform: uppercase;
  margin-top: 10rem;
  margin-left: 1rem;
  font-weight: bold;
}

.time-runner_time {
  font-size: 15rem;
  font-family: monospace;
  line-height: 0.85;
  color: #ff1b00;
  border-radius: 10px;

  &:not(:focus) {
    animation: outline-blink 0.3s linear infinite;
  }
}

.time-runner_table {
  margin-right: 1rem;
  position: relative;
}

.time-runner_top-teams {
  display: flex;
  flex-direction: row;
  align-items: end;
  justify-content: center;
  align-self: stretch;
  margin: 10rem 0 0 0;
  gap: 0;

  & > * {
    font-size: 5rem;
    padding: 1rem 2.5rem;
    font-weight: bold;
    color: white;
  }

  & .top-team--1 {
    order: 2;
    background-color: rgb(255, 191, 0);
    //margin-top: 0;
    padding-bottom: 20rem;
  }

  & .top-team--2 {
    order: 1;
    background-color: rgb(165, 165, 165);
    //margin-top: 7.5rem;
    padding-bottom: 12.5rem;
  }

  & .top-team--3 {
    order: 3;
    background-color: rgb(214, 105, 47);
    //margin-top: 15rem;
    padding-bottom: 5rem;
  }
}

.time-runner_controls {
  position: fixed;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-between;
  align-self: center;
  gap: 0.8rem;
  opacity: 0;
  margin-top: 10px;
  padding: 10px;
  background-color: #f2f2f2;
  border-radius: 10px;
  box-shadow: 0 4px 4px 0 rgba(0, 0, 0, 0.1);
  transition: opacity 0.2s ease-in-out;

  &:hover {
    opacity: 1;
  }
}

.time-runner_controls_row {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: space-between;
  align-self: stretch;
  gap: 0.8rem;
}

.time-runner_controls_start-time {
  font-weight: 600;
  user-select: all;
}

@keyframes outline-blink {
  0% {
    outline: #ff5500 solid 1rem;
  }
  //50% {
  //  outline: unset;
  //}
  100% {
    outline: #ff5500 solid 1rem;
  }
}
</style>