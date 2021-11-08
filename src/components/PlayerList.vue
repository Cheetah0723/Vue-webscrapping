<template>
  <div v-if="!homeTeam">Loading...</div>
  <div class="container mb-5">
  <div class="row">
    <div class="col-5">
      <h3 v-if="event" class="m-5">{{ event['home-team'].name }} </h3>
    </div>
    <div class="col-2">
      <h3 v-if="event" class="m-5">vs</h3>
    </div>
    <div class="col-5">
      <h3 v-if="event" class="m-5"> {{ event['away-team'].name }}</h3>
    </div>
  </div>
  <div class="row">
    <div class="col-5">
      <h5 v-if="event">Guessing Score: {{ event['home-team']['guessing-score'] }}</h5>
    </div>
    <div class="col-2">
      <h5 v-if="event"></h5>
    </div>
    <div class="col-5">
      <h5 v-if="event">Guessing Score: {{ event['away-team']['guessing-score']  }}</h5>
    </div>
  </div>
</div>
  <div class="row">
    <div class="col-6" v-if="homeTeam">
      <h4>Home Team</h4>
      <table class="table">
        <tr>
          <th colspan="3">Total Players G-Score</th>
          <td>{{ getPlayersTotalScore('home') }}</td>
        </tr>
        <tr>
          <th>#</th>
          <th colspan="2">Player Name</th>
          <th>Guessing Score</th>
          <th>Boost</th>
        </tr>
        <tr v-for="(player, index) in homeTeam.players" :key="index">
          <td>{{index + 1}}</td>
          <td colspan="2">{{player['player-name']}}</td>
          <td>{{player['guessing-score']['score']}}</td>
          <td>{{player['guessing-score']['boost']}}</td>
        </tr>
      </table>
    </div>
    <div class="col-6" v-if="awayTeam">
      <h4>Away Team</h4>
      <table class="table">
        <tr>
          <th colspan="3">Total Players G-Score</th>
          <td>{{getPlayersTotalScore('away') }}</td>
        </tr>
        <tr>
          <th>#</th>
          <th colspan="2">Player Name</th>
          <th>Guessing Score</th>
          <th>Boost</th>
        </tr>
        <tr v-for="(player, index) in awayTeam.players" :key="index">
          <td>{{index + 1 }}</td>
          <td colspan="2">{{player['player-name']}}</td>
          <td>{{player['guessing-score']['score']}}</td>
          <td>{{player['guessing-score']['boost']}}</td>
        </tr>

      </table>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  sum: 10,
  name: 'player-list',
  props: ['players', 'event', 'playerView', 'totalRequest', 'eventkey'],
  data () {
    return {
      data: null,
      awayTeam: null,
      homeTeam: null
    }
  },
  mounted () {
    console.log(this.eventkey, 'Getting data')
    this.getEvent(this.eventkey)
  },
  methods: {
    getEvent (eventkey) {
      axios.post('http://localhost/vue-apis-server/players.php?eventid=' + eventkey)
        .then(response => this.loadEvent(response))
    },
    loadEvent (response) {
      console.log(response)
      if (response.status === 200) {
        this.data = response.data
        this.homeTeam = response.data['home-team']
        this.awayTeam = response.data['away-team']
        console.log(this.homeTeam, this.awayTeam)
      }
    },
    getPlayersTotalScore (side) {
      let team = this.homeTeam
      let total = 0
      if (side === 'away') {
        team = this.awayTeam
      }
      for (const player of team.players) {
        total = total + player['guessing-score'].score
      }
      return total.toFixed(5)
    }
  },
  filters: {
    myDecimal: function (value) {
      return value.toFixed(2)
    }

  }
}
</script>
