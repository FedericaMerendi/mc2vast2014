<template>
  <div id="chart">
      <apexchart type="bar"
                 :options="chartOptions"
                 :series="series"></apexchart>
  </div>
</template>

<script>

export default {
  name: "ExpensesChart",
  props: {
    expenses: Array,
    categories: Array,
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
        }
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
    categories: function() {
      this.chartOptions = {...this.chartOptions, ...{
          xaxis: {
            categories: this.categories,
            labels: {
              style: {
                colors: ['red']
              }
            }
          }
        }}
    }
  }
}
</script>

<style scoped>

</style>