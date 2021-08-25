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
          enabled: false
        },
        xaxis: {
          categories: this.categories,
          labels: {
            rotate: 0,
          },
          title: {
            text: 'Level of aggregation',
            align: 'center',
          }
        },
        yaxis: {
          title: {
            text: 'Amount of money ($)'
          }
        },
        title: {
          text:this.title,
        },
      },
    }
  },
  watch: {
    expenses: function () {
      // In the same way, update the series option
      this.series = [{
        data: this.expenses
      }]
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
              }
            }
          },
          yaxis: {
            labels:{
              title: {
                text: 'Amount of money ($)'
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