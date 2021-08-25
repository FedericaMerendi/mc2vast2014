<template>
  <div>
    <apexchart id="timeline"
        type="rangeBar"
        height = '900'
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
      datalocations: [],
      chartOptions: {
        chart: {
          height: '100%',
          type: 'rangeBar',
          events: {
            zoomed: (chartContext, { xaxis })  => {
              this.updateTime(xaxis)
            },
            legendClick: (chartContext, seriesIndex) => {
              this.getLocation(seriesIndex)
            },
            dataPointSelection: (event, chartContext, config) => {
              let location = config.seriesIndex;
              let item = config.dataPointIndex;
              this.getDataPoint(location,item)

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
          function ({ seriesIndex, w }) {
           let colors = ['#003D30', '#005745', '#00735C', '#009175',
             '#00AF8E', '#00CBA7', '#00EBC1', '#86FFDE',
             '#00306F', '#00489E', '#005FCC', '#009FFA',
             '#00C2F9', '#00E5F8', '#7CFFFA', '#004002',
             '#005A01', '#007702', '#009503', '#AFFF2A',
             '#00D302', '#00F407', '#fde725', '#dce319ff',
             '#450d54','#482677', '#453781ff', '#404788ff']
            let len =w.config.series.length
            if(seriesIndex === len-1){ //gps
              return '#0079FA'
            } else if (seriesIndex === len-2) { ///loyaltyCard
              return '#00B408'
            } else {
                return colors[seriesIndex]
            }

          }
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
          position: 'bottom',
          horizontalAlign: 'left',
          fontSize: '12px',
          /*markers: {
            width: 7,
            height: 7,
          },*/
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
               return (
                   '<div><b>Driving</b> <br><b> Name</b>: ' + name + ' </b><br> <b>Driving</b>: <br>' + minutes + 'minutes </div>'
               )
             } else if (loc === 'Loyalty Card'){
               //let time = new Date(firstTime.getTime() + 2*60000);
               const data = this.getInfoLoyalty(firstTime, name);
               let price = data[0];
               let location = data[1];
               return (
                   '<div><b>Loyalty Card</b><br><b>Name</b>:' + name + '<br> <b>Value: </b>' + price + '$ <br><b>Location</b>:' + location + '</div>'
               )
             } else {
               let time = new Date(firstTime.getTime() + 4*60000);
               const price = this.getInfo(firstTime, name, loc);
               return (
                   '<div><b> Credit Card</b> <br><b>Name</b>: ' + name + '<br><b>Value: </b>' + price + '$ <br><b>Location</b>:' + loc + '</div><b>Time</b>:' + time.toLocaleTimeString() + '</div>'
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
      let location = this.locations[index]
      this.$emit('get-location', location);
    },
    getDataPoint(locationIndex, item) {
      let location = this.locations[locationIndex];
      if (location === 'Driving') {
        this.$emit('display-path', this.dataPaths[item].pathID);
      } else if (location === 'Loyalty Card') {
        this.$emit('update-loyalty-expenses', this.dataLC[item]);
      } else {
        this.$emit('update-expenses', this.datalocations[location][item]);
      }
    },
    updateTime(t) {
      this.$emit('update-time', [t.min,t.max]);
    },
    resetChart() {
      this.$emit('reset');
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
      this.datalocations = cc;
      const location = Object.keys(cc);
      location.sort();
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
      location.push('Loyalty Card', 'Driving');
      this.locations = location;
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
}
</script>

<style scoped>

</style>