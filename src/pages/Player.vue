<template>
  <div class="contents">
    <!-- <div class="contents__search">
      <search-input v-model="searchQuery" />
    </div> -->
    <div class="contents__header">
      <div class="page-name">Players</div>
    </div>
    <div v-if="!isPlayerLoading">
      <div v-if="getPlayer" class="card">
        <div class="card__top">
          <div class="top-container">
            <div class="contents__main">
              <div class="player-logo">
                <img class="player-logo__img" :src="getPlayerLogo(getPlayer.personId)" alt="Player's Logo">
              </div>
              <div class="player-names">
                <div class="player-names--cityname">
                  <div>
                    <p>{{ getPlayer.firstName }}</p>
                  </div>
                  <div>
                    <strong>{{ getPlayer.lastName }}</strong>
                  </div>
                </div>
                <div class="player-names--fullname">
                  <div>
                    <i>
                      <a>{{ getTeam(getPlayer.teamId).fullName }}</a>
                    </i>
                  </div>
                </div>
              </div>
            </div>
            <div class="player-specs">
              <div class="line vl--player"></div>
              <div class="specs">
                <div class="spec">
                  <img class="spec__icon" src="../assets/icons/height.svg" alt="Height Icon"/>
                  <div class="spec__text">{{ getPlayer.heightFeet + "'" + getPlayer.heightInches + "\""}}</div>
                </div>
                <div class="spec">
                  <img class="spec__icon" src="../assets/icons/weight.svg" alt="Weigth Icon"/>
                  <div class="spec__text">{{ getPlayer.weightPounds }}</div>
                </div>
                <div class="spec">
                  <img class="spec__icon" src="../assets/icons/pos.svg" alt="Position Icon"/>
                  <div class="spec__text">{{ getPlayer.pos }}</div>
                </div>
              </div>
            </div>
            </div>
          <div class="top-container">
            <router-link to="/players">
              <default-button class="players-button">All players</default-button>
            </router-link>
          </div>
        </div>
        <div class="hl hl--player"></div>
        <div class="card__bottom">
          <div class="card-bottom__container stats-container">
            <div class="card-bottom-container__header">Stats</div>
            <div class="stats">
              <div class="stat" v-for="stat in statsList" :key="stat.name">
                <div class="stat__name" :title="stat.placeholder">
                  {{ stat.title }}
                  <div class="hl hl--stat"></div>
                </div>
                <div class="stat__value">{{ stats.latest[stat.name] }}</div>
              </div>
            </div>
          </div>
          <div class="vl vl--bottom-containers"></div>
          <div class="card-bottom__container team">
            <div class="card-bottom-container__header">Team</div>
            <div class="team-info-and-divs">
              <div class="team-container team-info">
                <div class="team-info__main">
                  <div class="team-logo">
                    <img class="team-logo__img" :src="getTeamLogo(getPlayer.teamId)" alt="Team's Logo">
                  </div>
                  <div class="team-name">
                    {{ getTeam(getPlayer.teamId).fullName }}
                  </div>
                </div>
                <div class="team-info__params">
                  <div class="team-info-param">
                    <div class="param__name">Abbreviation</div>
                    <div class="param__value">{{ getTeam(getPlayer.teamId).tricode }}</div>
                  </div>
                  <div class="hl hl--params"></div>
                  <div class="team-info-param">
                    <div class="param__name">Conference</div>
                    <div class="param__value">{{ getTeam(getPlayer.teamId).confName }}</div>
                  </div>
                  <div class="hl hl--params"></div>
                  <div class="team-info-param">
                    <div class="param__name">Division</div>
                    <div class="param__value">{{ getTeam(getPlayer.teamId).divName }}</div>
                  </div>
                  <div class="hl hl--params"></div>
                  <router-link :to="'/teams/' + getTeam(getPlayer.teamId).urlName + ((year) ? ('?year=' + year) : '')">
                    <default-button class="team-button">See team profile</default-button>
                  </router-link>
                </div>
              </div>
              <div class="vl vl--team"></div>
              <div class="team-container divisions">
                <div class="conference">
                  <div class="conference__name conf-east">East</div>
                  <div class="vl vl--conf"></div>
                  <div class="divisions__names">
                    <div class="division">
                      <div class="division__color div-atlantic"></div>
                      <div class="division__name">Atlantic</div>
                    </div>
                    <div class="division">
                      <div class="division__color div-central"></div>
                      <div class="division__name">Central</div>
                    </div>
                    <div class="division">
                      <div class="division__color div-southeast"></div>
                      <div class="division__name">Southeast</div>
                    </div>
                  </div>
                </div>
                <div class="conference">
                  <div class="conference__name conf-west">West</div>
                  <div class="vl vl--conf"></div>
                  <div class="divisions__names">
                    <div class="division">
                      <div class="division__color div-southwest"></div>
                      <div class="division__name">Southwest</div>
                    </div>
                    <div class="division">
                      <div class="division__color div-northwest"></div>
                      <div class="division__name">Northwest</div>
                    </div>
                    <div class="division">
                      <div class="division__color div-pacific"></div>
                      <div class="division__name">Pacific</div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="no-players" v-else>
        <div class="no-players__text">Sorry, there are no players found...</div>
        <img class="no-players__img" src="../assets/svg/not-found.svg" />
      </div>
    </div>
    <div class="loading" v-else>
      <div class="loading__text">Loading...</div>
      <img class="loading__img" src="@/assets/svg/loading.svg" />
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      year: "2021",
      teams: [],
      players: [],
      stats: [],
      statsList: [
        {title: "PPG", name: "ppg", placeholder: "Points Per Game"},
        {title: "RPG", name: "rpg", placeholder: "Rebounds Per Game"},
        {title: "APG", name: "apg", placeholder: "Assists Per Game"},
        {title: "MPG", name: "mpg", placeholder: "Minutes Per Game"},
        {title: "TPG", name: "topg", placeholder: "Turnovers Per Game"},
        {title: "SPG", name: "spg", placeholder: "Steals Per Game"},
        {title: "BPG", name: "bpg", placeholder: "Blocks Per Game"},
        {title: "3P%", name: "tpp", placeholder: "3-Point Field Goal Percentage"},
        {title: "FT%", name: "ftp", placeholder: "Field Throw Percentage"},
        {title: "FG%", name: "fgp", placeholder: "Field Goal Percentage"},
        {title: "AST", name: "assists", placeholder: "Assists"},
        {title: "BLK", name: "blocks", placeholder: "Blocks"},
        {title: "STL", name: "steals", placeholder: "Steals"},
        {title: "TOV", name: "turnovers", placeholder: "Turnovers"},
        {title: "OREB", name: "offReb", placeholder: "Offensive Rebounds"},
        {title: "DREB", name: "defReb", placeholder: "Defensive Rebounds"},
        {title: "REB", name: "totReb", placeholder: "Rebounds"},
        {title: "FGM", name: "fgm", placeholder: "Field Goals Made"},
        {title: "FGA", name: "fga", placeholder: "Field Goals Attempted"},
        {title: "3PM", name: "tpm", placeholder: "3 Point Field Goals Made"},
        {title: "3PA", name: "tpa", placeholder: "3 Point Field Goals Attempted"},
        {title: "FTM", name: "ftm", placeholder: "Free Throws Made"},
        {title: "FTA", name: "fta", placeholder: "Free Throws Attempted"},
        {title: "PF", name: "pFouls", placeholder: "Personal Fouls"},
        {title: "PTS", name: "points", placeholder: "Points"},
        {title: "GP", name: "gamesPlayed", placeholder: "Games Played"},
        {title: "+/-", name: "plusMinus", placeholder: "Plus Minus"},
        {title: "MIN", name: "min", placeholder: "Minutes Played"},
        {title: "DD2", name: "dd2", placeholder: "Double Doubles"},
        {title: "TD3", name: "td3", placeholder: "Trible Doubles"}
      ],
      isTeamsFetched: false,
      isPlayerLoading: true,
      searchQuery: ''
    };
  },
  methods: {
    async fetchTeams(year) {
      axios
        .get("https://data.nba.net/data/10s/prod/v1/" + year + "/teams.json")
        .then((response) => {
          this.teams = response.data.league.standard;
          this.isTeamsFetched = true;
        });
    },
    async fetchPlayers(year) {
      axios
        .get("https://data.nba.net/data/10s/prod/v1/" + year + "/players.json")
        .then(response => {
          this.players = response.data.league.standard;
          this.isPlayerLoading = false;
        })
    },
    async fetchPlayerStats(year) {
      axios
        .get("https://data.nba.net/data/10s/prod/v1/" + year + "/players/" + this.$route.params.personId +"_profile.json")
        .then(response => {
          this.stats = response.data.league.standard.stats;
        })
    },
    getTeam(teamId){
      var resTeam;
      this.teams.forEach(team => {
        if(team.teamId == teamId) resTeam = team
      });
      return resTeam;
    },
    getTeamLogo(teamId){
      return 'https://avatars.dicebear.com/api/jdenticon/' + teamId + '.svg';
    },
    getPlayerLogo(personId){
      return 'https://avatars.dicebear.com/api/big-ears-neutral/' + personId + '.svg';
    },
  },
  mounted() {
    if(this.$route.query.year) this.year = this.$route.query.year;
    this.fetchTeams(this.year);
    this.fetchPlayers(this.year);
    this.fetchPlayerStats(this.year);
  },
  computed:{
    getPlayer(){
      var resPlayer = null;
      var BreakException = {};
      try{
        this.players.forEach((player) => {
          if (player.personId === this.$route.params.personId) {
            resPlayer = player;
            throw BreakException;
          }
        })
      } catch(e){
        if(e !== BreakException) throw e;
      }
      return resPlayer;
    },
  }
};
</script>

<style scoped>
.card {
  background: #f6f6f6;
  border: 2px solid #c4c4c4;
  box-shadow: 3px 3px 9px rgba(0, 0, 0, 0.25);
  border-radius: 24px;

  display: flex;
  flex-direction: column;

  padding: 13px 22px 13px 22px;
  margin: 25px 25px 0px 0px;

  align-items: center;

  width: 1500px;
  height: 765px;
}

.card__top {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;

  width: 100%;
  height: 225px;
}

.top-container{
  display: flex;
  flex-direction: row;
}

.contents__main {
  display: flex;
  flex-direction: row;
  align-items: center;
  width: 525px;
}

.player-logo {
  width: 165px;
  height: 165px;

  margin-right: 24px;

  background: #ececec;
  box-shadow: 2px 4px 5px 1px rgba(0, 0, 0, 0.25);
  border-radius: 88px;

  display: flex;
  justify-content: center;
  align-items: center;
}

.player-logo__img{
  width: 100%;
  border-radius: 50%;
}

.player-names {
  display: flex;
  flex-direction: column;
}

.player-names--cityname {
  font-size: 34px;
  line-height: 115%;

  color: #212121;
}

.player-names--fullname {
  margin-top: 14px;
  font-size: 22px;
  color: #6a6a6a;
}

.player-specs {
  display: flex;
  align-items: center;
  justify-content: center;
}

.players-button{
  margin-right: 50px;
}

.vl {
  border-left: 2px solid #c4c4c4;
  align-self: center;
}

.hl {
  border-bottom: 2px solid #c4c4c4;
  align-self: center;
}

.vl--specs {
  height: 142px;
}

.spec {
  margin: 22px;
  width: 140px;
  display: flex;
  flex-direction: row;
  align-items: center;
}

.spec__icon {
  width: 22px;
}

.spec__text {
  margin-left: 22px;

  color: #6a6a6a;
  font-size: 22px;
  line-height: 25px;
}

.no-players {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.no-players__text {
  font-size: 36px;
  font-weight: bold;
  color: #6a6a6a;
  margin: 50px;
}

.no-players__img {
  width: 300px;
}

.hl--player {
  margin: 15px 0 40px 0;
  width: 1400px;
}

.card__bottom {
  display: flex;
  flex-direction: row;

  width: 100%;
  height: 600px;
}

.vl--bottom-containers {
  height: 94%;
}

.card-bottom__container {
  display: flex;
  flex-direction: column;
}

.loading {
  margin: auto;
  font-size: 24px;

  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.loading__text {
  font-size: 36px;
  font-weight: bold;
  color: #6a6a6a;
  margin: 50px;
}

.loading__img {
  width: 200px;
}

.team {
  width: 720px;
}

.card-bottom-container__header {
  margin-left: 45px;
  margin-bottom: 20px;
  font-size: 24px;
  font-weight: bold;
  color: #212121;
}

.stats-container {
  width: 550px;
}

.stats {
  display: flex;
  flex-flow: row wrap;
  padding-left: 20px;

  height: 440px;
}

.stat {
  width: 33%;
  font-size: 24px;
  color: #6a6a6a;

  margin: 10px 0 0 0;

  display: flex;
  flex-direction: row;
}

.stat__name {
  width: 80px;
}

.stat__value{
  font-weight: bold;
}

.hl--stat {
  width: 160%;
}

.team-info-and-divs{
  display: flex;
  flex-direction: row;

  margin-top: 20px;
}

.team-logo {
  width: 75px;
  height: 75px;

  margin-right: 24px;

  background: #e6e6e6;
  box-shadow: 2px 4px 5px 1px rgba(0, 0, 0, 0.25);
  border-radius: 50%;

  display: flex;
  justify-content: center;
  align-items: center;
}

.team-logo__img {
  width: 45px;
}

.team-name{
  font-size: 24px;
  color: #6A6A6A;
  font-weight: bold;
  font-style: italic;
}

.team-info{
  margin-top: 30px;
  margin-left: 50px;
}

.team-info__main{
  display: flex;
  flex-direction: row;
  align-items: center;
}

.team-info__params{
  margin-top: 20px;
  margin-left: 100px;
  width: 250px;
  font-size: 24px;
  color: #6A6A6A;
}

.team-info-param{
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}

.param__name{
  margin-left: 10px;
}

.param__value{
  font-weight: bold;
  margin-right: 10px;
}

.hl--params{
  margin-bottom: 13px;
}

.team-button{
  margin-left: 25px;
  margin-top: 19px;
}

.vl--team{
  margin: 0 50px 0 70px;
  height: 100%;
}

.divisions{
  margin-top: 20px;
}

.conference{
  display: flex;
  flex-direction: row;
  align-items: center;

  font-size: 24px;

  margin-bottom: 30px;
}

.conference__name{
  font-weight: bold;
  width: 55px;
}

.conf-east{
  color: #055790;
}

.conf-west{
  color: #B90A34;
}

.vl--conf{
  margin: 0 12px 0 24px;
  height: 150px;
}

.divisions__names{
  display: flex;
  flex-direction: column;
}

.division{
  display: flex;
  flex-direction: row;
  align-items: center;

  margin-top: 5px;
  margin-bottom: 5px;
}

.division__name{
  color: #6A6A6A;
  margin-left: 16px;
}

.division__color{
  width: 40px;
  height: 40px;
}

.div-atlantic{
  background: #055790;
}
.div-central{
  background: #006BB7;
}
.div-southeast{
  background: #57A3D9;
}
.div-southwest{
  background: #B90A35;
}
.div-northwest{
  background: #ED174B;
}
.div-pacific{
  background: #FA4F79;
}


</style>