<template>
  <div class="container">
    <h1>Covid 19 {{country}}</h1>
    <h3>Najnoviji podaci ({{lastChecked}}):</h3>
    <div v-if="!cities" class="mb-5">
      <div class="row mt-3" >
        <div class="col-md-4">
          <div class="card text-white bg-warning" >
            <div class="card-body">
              <h5 class="card-title">{{covidStats.confirmed}}</h5>
              <p class="card-text">Broj zaraženih</p>
            </div>
          </div>
        </div>
        <div class="col-md-4">
          <div class="card text-white bg-danger" >
            <div class="card-body">
              <h5 class="card-title">{{covidStats.deaths}}</h5>
              <p class="card-text">Broj smrtnih slučajeva</p>
            </div>
          </div>
        </div>
        <div class="col-md-4">
          <div class="card text-white bg-success">
            <div class="card-body">
              <h5 class="card-title">{{covidStats.recovered}}</h5>
              <p class="card-text">Broj izlečenih</p>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="mb-5" v-else>
      <div class="row mt-3 ">
        <div class="clearfix"></div>
        <div class="col-md-4">
          <div class="card text-white bg-warning" >
            <div class="card-body">
              <h5 class="card-title">{{ukupno.ukupnoZarazenih}}</h5>
              <p class="card-text">Ukupno zaraženih</p>
            </div>
          </div>
        </div>
        <div class="col-md-4">
          <div class="card text-white bg-danger" >
            <div class="card-body">
              <h5 class="card-title">{{ukupno.ukupnoUmrlih}}</h5>
              <p class="card-text">Ukupno umrlih</p>
            </div>
          </div>
        </div>
        <div class="col-md-4">
          <div class="card text-white bg-success">
            <div class="card-body">
              <h5 class="card-title">{{ukupno.ukupnoIzlecenih}}</h5>
              <p class="card-text">Ukupno izlečenih</p>
            </div>
          </div>
        </div>
      </div> <hr>
      <div class="row mt-3 " v-for="covidStat in covidStatsComputed" :key="covidStat.city">
        <div class="col-12">{{covidStat.city == '' ? covidStat.province : covidStat.city }}:</div>
        <div class="clearfix"></div>
        <div class="col-md-4">
          <div class="card text-white bg-warning" >
            <div class="card-body">
              <h5 class="card-title">{{covidStat.confirmed}}</h5>
              <p class="card-text">Broj zaraženih</p>
            </div>
          </div>
        </div>
        <div class="col-md-4">
          <div class="card text-white bg-danger" >
            <div class="card-body">
              <h5 class="card-title">{{covidStat.deaths}}</h5>
              <p class="card-text">Broj smrtnih slučajeva</p>
            </div>
          </div>
        </div>
        <div class="col-md-4">
          <div class="card text-white bg-success">
            <div class="card-body">
              <h5 class="card-title">{{covidStat.recovered}}</h5>
              <p class="card-text">Broj izlečenih</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'CovidChart',
  props: {
    msg: String
  },
  data: function() {
    return {
        country: 'Serbia',
        covidStats: {},
        cities: false,
        lastChecked: '',
    }
  },
  mounted() {
    let th = this;
    fetch("https://covid-19-coronavirus-statistics.p.rapidapi.com/v1/stats?country="+th.$data.country, {
        "method": "GET",
        "headers": {
        "x-rapidapi-host": "covid-19-coronavirus-statistics.p.rapidapi.com",
        "x-rapidapi-key": "af3dd482cfmsh4db5042cbc5464dp17fa13jsnc3ab02c11e89"
      }
    })
    .then(response => {
      response.json().then(res => {
        th.$data.covidStats = res.data.covid19Stats[0];

        if(res.data.covid19Stats.length > 1){
          th.$data.cities = true;
          th.$data.covidStats = res.data.covid19Stats;
        }

        let d =  new Date(res.data.lastChecked);
        let ye = new Intl.DateTimeFormat('en', { year: 'numeric' }).format(d)
        let mo = new Intl.DateTimeFormat('en', { month: 'short' }).format(d)
        let da = new Intl.DateTimeFormat('en', { day: '2-digit' }).format(d)
        th.$data.lastChecked = da + ". " + mo + ". "+ye+"" ;
      })
    })
    .catch(() => {
      alert("error")
    });
  },
  computed: {
    covidStatsComputed: function() {
      return this.$data.covidStats.filter(stat => stat.province != 'Recovered')
    },
    ukupno: function() {
      let ukupnoZarazenih = 0;
      let ukupnoUmrlih = 0;
      let ukupnoIzlecenih = 0;
      this.covidStatsComputed.forEach((element) => {
        ukupnoZarazenih += element.confirmed;
        ukupnoUmrlih += element.deaths;
        ukupnoIzlecenih += element.recovered;
      });
      return {
        'ukupnoZarazenih': ukupnoZarazenih,
        'ukupnoUmrlih': ukupnoUmrlih,
        'ukupnoIzlecenih': ukupnoIzlecenih
      };
    }
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
