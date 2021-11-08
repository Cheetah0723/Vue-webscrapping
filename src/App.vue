<template>
  <div id="app" class="container">
    <button class="btn btn-secondary" v-on:click="selectedEvent=null" v-if="selectedEvent">Back</button>
    <div v-if="!selectedEvent">
      <h1>Sport Forcaster</h1>
      <h2>Events</h2>
      <table class="table" style="min-width: 600px;">
        <thead>
        <tr>
          <th scope="col">Game</th>
          <th scope="col">Date</th>
          <!--<th scope="col">Boost</th>-->
          <th scope="col">Score</th>
          <!--<th scope="col">Score Sum</th>-->
          <th>Actions</th>
        </tr>
        </thead>
        <tbody>
        <tr v-for="(event, index) in events" :key="index">
          <td>{{ event['home-team'].name }}({{ event['home-team'].abbreviation }}) vs {{ event['away-team'].name }}({{ event['away-team'].abbreviation }})</td>
          <td>{{ event['event-date'] }} {{ event['event-time'] }}</td>
          <td >{{ event['home-team']['actual-score'] }} - {{ event['away-team']['actual-score'] }}</td>
          <td><button class="btn btn-primary btn-sm" v-on:click="loadEvent(event)">Players</button></td>
        </tr>
        </tbody>
      </table>
    </div>
    <player-view :player="playerView" v-if="playerView" />
    <player-list :eventkey="selectedEvent['event-key']" :event="selectedEvent" :players="players" :playerView="playerView" v-if="selectedEvent" />
  </div>
</template>

<script>
import PlayerView from './components/PlayerView.vue'
import PlayerList from './components/PlayerList.vue'

import axios from 'axios'

export default {

  name: 'App',
  data () {
    return {
      playerView: null,
      players: null,
      events: null,
      selectedEvent: null
    }
  },
  created () {
    axios({
      method: 'get',
      url: 'https://staging.formula.forecaster.ca/api/schedules?season-id=2021&start_date=2021-11-16%2018:59:00&end_date=2021-11-16%2019:01:00'
    })
      .then(responce => (this.players = responce.data))
      .catch(error => console.log(error))
  },
  components: {
    PlayerView,
    PlayerList
  },
  computed: {
    totalRequest () {
      return this.players.reduce((acc, item) => acc + item.player.guessing_score.score, 0)
    }
  },
  mounted () {
    this.getEvents()
  },
  methods: {
    getEvents () {
      console.log('Loaded')
      axios.post('http://localhost/vue-apis-server/events.php')
        .then(response => this.loadEvents(response))
    },
    loadEvent (event) {
      console.log(event['event-key'])
      this.selectedEvent = event
    },
    loadEvents (response) {
      if (response.status === 200) {
        this.events = response.data.events
        console.log(this.events)
      }
    }
  }

}

</script>

<style>
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }

</style>
