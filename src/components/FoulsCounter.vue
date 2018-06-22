<template>
  <div class="fouls-counter">
    <h1>World Cup 2018</h1>
    <h3>Total fouls:
      <span class="counter">{{ total_fouls_count }}</span>
    </h3>
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

  totalFouls() {
    return this.totalStatistic('fouls_committed');
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

  totalFoulsCount() {
    return this.getCompleted().reduce((accumulator, match) => accumulator + match.totalFouls(), 0);
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
      return this.matches.totalFoulsCount();
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
