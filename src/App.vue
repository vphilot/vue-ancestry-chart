<template>
  <div id="app">
    <div class="intro">
      <h2>Vue Ancestry Chart</h2>
      <p>Hover / click on the Doughnut to interact with the datasets.</p>
      <p>Click on a legend item to remove it from the chart.</p>
    </div>
      <AncestryChart :chartData="this.dataset" :options="this.options" width=500 height=1000 css-classes='chart-container' />
  </div>
</template>

<script>

import * as d3 from '../node_modules/d3-fetch/dist/d3-fetch'
import AncestryChart from './components/AncestryChart.vue'
import { type } from 'os';


 export default {
  name: 'app',
  components: {
    AncestryChart
  },

  data() {
    return {
      dataset: {},
      options: {
        maintainAspectRatio: false,
        responsive: true,
        rotation: -0.75 * Math.PI,
        layout: {
          padding: 0
        },
        animation: {
          duration: 1000
        },
        tooltips: {
          enabled: true
        },
        legend: {
          position: 'bottom',
          align: 'start',
          labels: {
            fontSize: 14,
            fontFamily: 'IBM Plex Mono',
            fontColor: '#999999',
            usePointStyle: true,
            padding: 12
          }
        },
      }
    }
  },

  methods: {
    
    readCSV(csvFile) {
      d3.csv(csvFile).then( data => this.parseData(data) );
    },

    parseData(data) {
      //parse raw csv data and get ancestry count
      let composition = {};
      data.map( (item, index) => {
        
        if(!composition.hasOwnProperty(item.Ancestry)) 
        {
          composition[item.Ancestry] = 1;
        } else {
          composition[item.Ancestry] += 1;
        }
      });

      //format data for the chart
      let d = {
        labels: [],
        datasets: [{
          label: 'Ancestry:',
          data: [],
          backgroundColor: [],
          borderColor: [],
          borderAlign: 'inner',
          borderWidth: 0.5,
        }]
      };

      //calculating totals for percentage purposes
      let total = 0;

      for (let key in composition) {
        total += composition[key];
      }

      //trying to sort this

      let compositionSorted = [];

      for (let key in composition){
        compositionSorted.push({ancestry: key, count: composition[key]})
      }

      compositionSorted.sort(function(a, b) { 
        return a.count -+b.count;
      })
     
     compositionSorted.reverse();

      //counter is only for colour picking
      let counter = 0;

     compositionSorted.map(item => {
        d.labels.push(item.ancestry);
        d.datasets[0].data.push((item.count/total*100).toFixed(2));
        d.datasets[0].backgroundColor.push(this.getColor(counter));
        d.datasets[0].borderColor.push('rgba(0,0,0,0.5)');
        counter ++;
     })

      this.dataset = d;

    },

    getColor(iterator)  {
      let h = Math.abs(360-(iterator * 10));
      let s = Math.abs(100-iterator*2);
      let l = 70;
      console.log(`hsl(${h},${s}%,${l}%)`);
      return `hsl(${h},${s}%,${l}%)`;

    }
  },

  mounted() {
    this.readCSV('ancestry_composition.csv');
  }
}
</script>

<style>

body {
  width: 100%;
  margin: 0;
  padding-top: 25px;
  background-color: #000000;
}

#app {
  color: #dddddd;
  font-family: 'IBM Plex Mono';
  background-color: #000000;
  width: 100%;
  height: auto;
}

.chart-container, .intro{
  width: 80% !important;
  margin: 0 auto;
}

/* tablet */
@media (min-width: 768px) {
  #app {
    max-width: 1000px;
    margin: 0 auto;
  }
}
/* desktop */
@media (min-width: 992px) {}


</style>
