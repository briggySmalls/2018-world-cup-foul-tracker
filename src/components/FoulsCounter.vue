<template>
  <div class="fouls-counter">
    <h1>2018 World Cup Foul Tracker</h1>
    <h2>The beautiful game</h2>
    <ol>
      <li>Total fouls: <span class="counter">{{ total_fouls_count }}</span></li>
      <li>Total yellow cards: <span class="counter">{{ total_yellow_cards }}</span></li>
      <li>Total red cards: <span class="counter">{{ total_red_cards }}</span></li>
    </ol>

  </div>
</template>

<script>
import request from 'request';
import assert from 'assert';

const ENDPOINT = "https://worldcup.sfg.io/matches/";

class Match {
  constructor(json) {
    this.data = json;
  }

  status() {
    return this.data.status;
  }

  totalStatistic(statistic) {
    assert.equal(this.data.status, "completed")
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

  getCompleted() {
    return this.matches.filter(match => match.status() === "completed");
  }

  totalStatistic(statistic) {
    return this.getCompleted().reduce((accumulator, match) => {
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
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
