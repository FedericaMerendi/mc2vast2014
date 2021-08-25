<template>
  <div id="chart">
      <apexchart type="bar"
                 :options="chartOptions"
                 :series="series"
                 ></apexchart>
  </div>
</template>

<script>

export default {
  name: "ExpensesChart",
  props: {
    expenses: Array,
    categories: Array,
    title: String,
  },
  data() {
    return {
      series: [{
        data: this.expenses,
      }],
      chartOptions: {
        chart: {
          type: 'bar',
          height: '90%'
        },
        plotOptions: {
          bar: {
            borderRadius: 0,
            horizontal: false,
          }
        },
        dataLabels: {
          enabled: true
        },
        xaxis: {
          categories: this.categories,
          labels: {
            rotate: 0,
            style: {
              fontSize: '10px',
            }
          },
          title: {
            text: 'Level of aggregation',
            align: 'center',
            fontSize: '13px',
          }
        },
        yaxis: {
          title: {
            text: 'Amount of money ($)',
            fontSize: '13px',
          }
        },
        title: {
          text:this.title,
        },
        tooltip: {
          x:{
            show: false,
          }
        },
        colors:  [
          function ({ dataPointIndex, w }) {
          let len =w.config.series[0].data.length
          if(len === 1){
            return '#0072B2'
          } else if (len ===2){
            if (dataPointIndex == 0) {
              return '#56B4E9';
            } else {
              return '#0072B2';
            }
          } else {
            if (dataPointIndex == 0) {
              return '#009E73';
            } else if (dataPointIndex == 1) {
              return '#56B4E9';
            } else {
              return '#0072B2'
            }
          }
          }
        ]
            //'#0072B2', '#56B4E9','#009E73'],
      },
    }
  },
  watch: {
    expenses: function () {
      // In the same way, update the series option
      this.series = [{
        data: this.expenses
      }];
      this.chartOptions = {
        ...this.chartOptions, ...{
          yaxis: {
            labels:{
              title: {
                text: 'Amount of money ($)',
                fontSize: '13px',
              }
            }
          },
        }
      }
    },
    categories: function () {
      this.chartOptions = {
        ...this.chartOptions, ...{
          xaxis: {
            categories: this.categories,
            labels: {
              rotate: 0,
              title: {
                text: 'Level of aggregation',
                fontSize: '13px',
              }
            }
          },
          yaxis: {
            labels:{
              title: {
                text: 'Amount of money ($)',
                fontSize: '13px',
              }
            }
          },
        }
      }
    }
  }
}
</script>

<style scoped>
div {
  padding: 5px;
}
</style>