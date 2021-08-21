<template>
  <div>
    <apexchart id="timeline"
        type="rangeBar"
        height="800"
        :options="chartOptions"
        :series="series()"></apexchart>

  </div>
</template>

<script>

export default {
  name: "EventTimeline",
  props: {
    dataCC: Array,
    dataLC: Array,
    dataPaths: Array,
  },
  data() {
    return {
      //data: ,
      locations: [],
      chartOptions: {
        chart: {
          height: 800,
          type: 'rangeBar',
          events: {
            zoomed: (chartContext, { xaxis })  => {
              this.updateTime(xaxis)
            },
            legendClick: (chartContext, seriesIndex, config) => {
              console.log(chartContext)
              console.log(seriesIndex)
              console.log(config)
              this.getLocation(seriesIndex)
            },
            dataPointSelection: (event, chartContext, config) => {
              console.log(event);
              console.log(chartContext);
              console.log(config);
              console.log(config.dataPointIndex);
              this.getDataPoint(config.dataPointIndex)
            },
          }

        },
        plotOptions: {
          bar: {
            horizontal: true,
            barHeight: '50%',
            rangeBarGroupRows: true
          }
        },
        colors: [
          "#008FFB", "#00E396", "#FEB019", "#FF4560", "#775DD0",
          "#3F51B5", "#546E7A", "#D4526E", "#8D5B4C", "#F86624",
          "#D7263D", "#1B998B", "#2E294E", "#F46036", "#E2C044"
        ],
        fill: {
          type: 'solid'
        },
        xaxis: {
          type: 'datetime',
          position:'top',
          labels: {
            datetimeUTC: false
          }
        },
        legend: {
          position: 'left',
          horizontalAlign: 'right',
          fontSize: '12px',
          markers: {
            width: 7,
            height: 7,
          },
        },
        tooltip: {
           custom: (opts) => {
             let firstTime = new Date(opts.y1);
             let lastTime = new Date(opts.y2);
             let name = opts.ctx.rangeBar.getTooltipValues(opts).ylabel.replace(':','');
             let loc = opts.ctx.rangeBar.getTooltipValues(opts).seriesName.replace(':','').trim();

             if (loc === 'Driving') {
               let diff = Math.abs(lastTime - firstTime);
               let minutes = Math.floor((diff / 1000) / 60);
               return '<div><b-card><b-card-text>' + name + ' <br> drove for ' + minutes + 'minutes' +
                   '</b-card-text></b-card></div>'
             } else if (loc === 'Loyalty Card'){
               //let time = new Date(firstTime.getTime() + 2*60000);
               const data = this.getInfoLoyalty(firstTime, name);
               let price = data[0];
               let location = data[1];
               return (
                   '<div class="arrow_box">' + name + ' <br> used the loyalty card  <br> of value: ' + price + '$ <br> at ' + location + '</div>'
               )
             } else {
               let time = new Date(firstTime.getTime() + 4*60000);
               const price = this.getInfo(firstTime, name, loc);
               return (
                   '<div class="arrow_box">' + 'At ' + time.toLocaleTimeString() + ' <br> ' + name + ' spent  <br>' + price + '$ at ' + loc + '</div>'
               )
             }
           }
        }
      }
    }
  },
  computed: {
  },
  methods: {
    getLocation(index) {
      console.log(this.locations[index])
    },
    getDataPoint(index) {
      console.log(this.dataCC[index]);
    },
    updateTime(t) {
      this.$emit('update-time', [t.min,t.max]);
    },
    getInfoLoyalty(time,name){
      for (let i = 0; i < this.dataLC.length; i++) {
        let t = this.dataLC[i].timestamp.getTime() - 2*60000
        if ((this.dataLC[i].fullName === name) && (t === time.getTime())) {
          return [this.dataLC[i].price,this.dataLC[i].location];
        }
      }
    },
    getInfo(time,name,loc){
       for (let i = 0; i < this.dataCC.length; i++) {
         let t = this.dataCC[i].timestamp.getTime() - 4*60000
         if ((this.dataCC[i].fullName === name) && (this.dataCC[i].location === loc) && (t === time.getTime())) {
            return this.dataCC[i].price;
         }
      }
    },
    series(){
      let gps = this.gps();
      let lc = this.lc();
      let list = this.cc();
      list.push(lc);
      list.push(gps);
      return list
    },
    gps() {
      let obj = { name: 'Driving', data:[]}
      for (let i = 0; i < this.dataPaths.length; i++) {
        let  data =
            {
              x: this.dataPaths[i].fullName,
              y: [
                this.dataPaths[i].minTimestamp.getTime(),
                this.dataPaths[i].maxTimestamp.getTime()
              ]
            };
        obj['data'].push(data);
      }
      return obj
    },
    cc(){
      let cc = this.dataCC.reduce(function (r, a) {
        r[a.location] = r[a.location] || [];
        r[a.location].push(a);
        return r;
      }, Object.create(null));

      const location = Object.keys(cc);
      this.locations = location;
      let series = []
      location.forEach((location) => {
        let obj = { name: location,
                    data:[]}
        for (let i = 0; i < cc[location].length; i++) {
          let  data =
              {
                x: cc[location][i].fullName,
                y: [
                  cc[location][i].timestamp.getTime() -4*60000,
                  cc[location][i].timestamp.getTime() +4*60000,
                ],
              };

          obj['data'].push(data);
        }
        series.push(obj);
      });

      return series
    },
    lc() {
      let obj = { name: 'Loyalty Card', data:[]}
      for (let i = 0; i < this.dataLC.length; i++) {
        let  data =
            {
              x: this.dataLC[i].fullName,
              y: [
                this.dataLC[i].timestamp.getTime() -2*60000,
                this.dataLC[i].timestamp.getTime()  +2*60000,
              ]
            };
        obj['data'].push(data);
      }
      return obj
    },

  },
/*  watch: {
    paths: function () {
      this.gps()
    },
    dataCC: function () {
      this.cc()
    },
  },*/

}
</script>

<style scoped>

</style>