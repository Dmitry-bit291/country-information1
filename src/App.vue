<template>
  <div id="app">
    <md-button
      class="md-raised md-primary"
      @click="alldata"
      :disabled="disabledbutton"
      >Get all Countryes</md-button
    >
    <md-button
      class="md-raised md-accent"
      @click="cleardata"
      :disabled="disabledclear"
      >clear list</md-button
    >
    <md-field class="inputfield">
      <label>Initial Value</label>
      <md-input v-model="initial"></md-input>
    </md-field>
    <md-button class="md-icon-button" @click="searchdata">
      <md-icon>search</md-icon>
    </md-button>
    <md-list class="md-triple-line" v-if="Countryes">
      <md-list-item v-for="country in Countryes" :key="country.name">
        <md-avatar>
          <img :src="country.flag" alt="People" />
        </md-avatar>

        <div class="md-list-item-text">
          <span>{{ country.name }}</span>
          <span>population:{{ country.population }}</span>
          <span
            >region:{{ country.region }}<br />subregion:{{country.subregion}}</span>{{country.Countryes}}
            <span v-for="bordercountry in country.Countryes " :key="bordercountry.name">{{bordercountry.name}},</span>
        </div>

        <md-button class="md-icon-button md-list-action">
          <md-icon class="md-primary">star</md-icon>
        </md-button>
      </md-list-item>
    </md-list>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "App",
  components: {},
  data() {
    return {
      Countryes: [],
      pool: 0,
      disabledbutton: false,
      initial: "",
    };
  },
  computed: {
    disabledclear() {
      return this.pool == 0;
    },
  },
  methods: {
    cleardata() {
      this.Countryes.splice(0, this.Countryes.length);
      this.pool = 0;
    },
    async searchdata() {
      this.Countryes.splice(0, this.Countryes.length);
      this.pool = 0;
      await axios
        .get(`https://restcountries.eu/rest/v2/name/${this.initial}`)
        .then(async (response) => {
          this.Countryes = response.data;
          console.log(this.Countryes);
          await this.Countryes.forEach(async (country) => {
            let codes = `https://restcountries.eu/rest/v2/alpha?codes=`;
            await country.borders.forEach(async (border) => {
              codes += border + ";";
            });
            await axios.get(codes)
            .then(response=> {
              console.log(response) 
              let c = response.data
              country.Countryes=[]
              c.forEach(cc => {
              country.Countryes.push(cc)
              })
            })
          });
          console.log(this.Countryes)
          // handle success
          /*console.log(this.pool)
          for(let i = this.pool ; response.data.length + this.pool ; i ++) {
            //console.log(response.data[i]);
            if(i < response.data.length)
            this.Countryes.push(response.data[i])
            else {
              this.disabledbutton = true
            }
          }
            response.data.length*/
        })
        .catch(function (error) {
          // handle error
          console.log(error);
        });
    },
    async alldata() {
      await axios
        .get("https://restcountries.eu/rest/v2/all")
        .then((response) => {
          // handle success
          console.log(this.pool);
          for (let i = this.pool; i < 10 + this.pool; i++) {
            //console.log(response.data[i]);
            if (i < response.data.length) this.Countryes.push(response.data[i]);
            else {
              this.disabledbutton = true;
            }
          }
          this.pool += 10;
        })
        .catch(function (error) {
          // handle error
          console.log(error);
        });
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 40px;
}
.inputfield {
  width: 40%;
}
</style>
