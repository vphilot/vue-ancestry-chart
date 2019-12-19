
<script>

import { Doughnut, mixins }  from 'vue-chartjs'

export default {
  name: 'AncestryChart',
  extends: Doughnut,
  mixins: [mixins.reactiveProp],


props: {
  chartData: {},
  options: {}
},

  mounted() {
    this.renderChart(this.chartData, this.options)
  }
}


</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
