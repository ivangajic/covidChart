<template>
  <div class="home">
    <CovidChart />
  </div>
</template>

<script>
// @ is an alias to /src
import CovidChart from '@/components/CovidChart.vue'
import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'
export default {
  name: 'Home',
  components: {
    CovidChart
  }
}
</script>
