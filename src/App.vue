<template>
  <div id="app">
          <vue-chart type="bar" :data="chartData"></vue-chart>
      <div class="container">
          <vue-chart type="bar" :data="openChart"></vue-chart>
      </div>
  </div>
</template>

<script>
import VueChart from 'vue-chart-js'
export default {
  name: 'app',
  props: {
    profile: {
        type: String,
        default: '0'
    }
  },
  components: {
     VueChart
  },
    data : function () {
        return {
            graphClose: [],
            graphCloseLastTorque : [],
            graphCloseAverageTorque : [],
            graphCloseLabels : [],
            graphOpen : [],
            graphOpenLastTorque : [],
            graphOpenAverageTorque: [],
            graphOpenLabels : [],

            openChart: {
                labels: this.graphOpenLabels,
                datasets: [
                    {
                        label: 'Average open torque',
                        data: this.graphOpenAverageTorque
                    },
                    {
                        label: 'Last open torque',
                        data: this.graphOpenLastTorque
                    },
                    {
                        label: 'Forecast open torque',
                        data: [30, 50, 10]
                    }
                ]
            },
            chartData: {
                labels: ['Item 1', 'Item 2', 'Item 3'],
                datasets: [
                    {
                        label: 'Component 1',
                        data: [10, 20, 30]
                    },
                    {
                        label: 'Component 2',
                        data: [20, 30, 40]
                    }
                ]
            }
        }
    },
        created: function() {
        this.$http.get('http://wb-predictivemaintenance-api.prsp7vkew2.eu-central-1.elasticbeanstalk.com/api/TorqueValues')
                .then(function(response){
                    // {"Direction":"Close","Position":1,"AverageTorque":69,"LastTorque":69,"AssetId":"","Profile":1,"id":"5750496c224bf61c268c8887"},
                    console.log('resp = ',response.data);
                    var obj = response.data, current;
                    for(var key in obj){
                        current = obj[key];
                        if(current.Profile == this.profile){
                            if(current.Direction == 'Close'){
                                this.graphClose.push(current);
                                this.graphCloseLastTorque.push(current.LastTorque);
                                this.graphCloseAverageTorque.push(current.AverageTorque);
                                this.graphCloseLabels.push(current.Position);
                            } else {
                                this.graphOpen.push(current);
                                this.graphOpenLastTorque.push(current.LastTorque);
                                this.graphOpenAverageTorque.push(current.AverageTorque);
                                this.graphOpenLabels.push(current.Position);

                                this.openChart.datasets.push({data: [current.AverageTorque]});
                            }
                        }
                    }
                    console.log('data = ',this.graphOpenLabels);
                    //this.chartData.datasets.push({label: 'Component 12', data: [29, 39, 49]});
                });
    }
}
</script>

<style>
</style>
