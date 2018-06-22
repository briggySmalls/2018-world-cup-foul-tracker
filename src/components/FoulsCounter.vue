<template>
  <div class="fouls-counter">
    <h1 class="mb-2">2018 World Cup Foul Tracker</h1>
    <h2>The beautiful game</h2>
    <div class="container-fluid mt-2">
      <div class="row">
        <div class="col-md-4">
          <div class="card">
            <div class="card-body">
              <div class="card-title">Total fouls</div>
              <span class="counter">{{ total_fouls_count }}</span>
            </div>
          </div>
        </div>
        <div class="col-md-4">
          <div class="card">
            <div class="card-body">
              <div class="card-title">Total yellow cards</div>
              <span class="counter">{{ total_yellow_cards }}</span>
            </div>
          </div>
        </div>
        <div class="col-md-4">
          <div class="card">
            <div class="card-body">
              <div class="card-title">Total red cards</div>
              <span class="counter">{{ total_red_cards }}</span>
            </div>
          </div>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
import request from 'request';
import assert from 'assert';
import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'

const ENDPOINT = "https://worldcup.sfg.io/matches/";

class Match {
  constructor(json) {
    this.data = json;
  }

  status() {
    return this.data.status;
  }

  totalStatistic(statistic) {
    assert.notEqual(this.data.status, "future")
    return (
      this.data.home_team_statistics[statistic] +
      this.data.away_team_statistics[statistic]);
  }
}

class MatchesManager {
  constructor(data) {
    this.data = JSON.parse(data);

    this.matches = [];
    for (let matchData of this.data) {
      this.matches.push(new Match(matchData));
    }
  }

  getValid() {
    return this.matches.filter(match => match.status() !== "future");
  }

  totalStatistic(statistic) {
    return this.getValid().reduce((accumulator, match) => {
      return accumulator + match.totalStatistic(statistic);
    }, 0);
  }
}

export default {
  name: 'FoulsCounter',
  data: function () {
    return {
      matches: new MatchesManager('[]')
    };
  },
  computed: {
    total_fouls_count: function () {
      return this.matches.totalStatistic('fouls_committed');
    },
    total_yellow_cards: function () {
      return this.matches.totalStatistic('yellow_cards');
    },
    total_red_cards: function () {
      return this.matches.totalStatistic('red_cards');
    }
  },
  created: function () {
    let self = this;
    request(ENDPOINT, (error, response, body) => {
      self.matches = new MatchesManager(body);
    });
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
.counter {
  font-size: 9em
}
</style>
