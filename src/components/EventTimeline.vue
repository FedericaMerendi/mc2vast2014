<template>
  <div>
    <svg
        class='timechart'
        transform="translate(0,0)"
        :viewBox="viewBox">
      <g :transform="`translate(0, 0)`" >
        <g class="x-axis"
           :transform="`translate(${margin.left}, ${margin.top})`"></g>
        <g class="y-axis"
           :transform="`translate(${margin.left }, ${margin.top + r})`"></g>

        <g>
          <g class="x-grid grid" :transform="`translate(${margin.left}, ${margin.top})`"></g>
          <g class="y-grid grid" :transform="`translate(${margin.left}, ${margin.top})`"></g>
        </g>

        <g :transform="`translate(${margin.left}, ${margin.top +r})`">
          <rect
              :transform="`translate(0, 0)`"
              v-for="(p, i) in dataPaths"
              :key="i"
              :height="r/2"
              fill="blue"
              :width="valueScaleX(p.maxTimestamp) - valueScaleX(p.minTimestamp)"
              :x="valueScaleX(p.minTimestamp)"
              :y="valueScaleY(p.fullName)"
              @click="getInfoPath(p)"/>
        </g>

        <!--  -->
        <g :transform="`translate(${margin.left}, ${margin.top + r})`">
          <rect
              :transform="`translate(0, 0)`"
              v-for="(cc, i) in dataCC"
              :key="i"
              :height="r"
              :width="r"
              :x="valueScaleX(cc.timestamp)"
              :y="valueScaleY(cc.fullName)"
              fill="pink"
              @click="getInfoCC(cc)"/>
        </g>

       <!-- <g :transform="`translate(${margin.left}, ${margin.top + r})`">
          <circle
              :transform="`translate(0, 0)`"
              v-for="(lc, i) in dataLC"
              :key="i"
              :r="r/2"
              cx="10"
              :cy="valueScaleY(lc.fullName)"
              fill="blue"
              @click="getInfoCC(lc)"></circle>
        </g>
-->
      </g>
    </svg>
  </div>
</template>

<script>
const d3 = require('d3');

export default {
  name: "EventTimeline",
  props: {
    dataCC: Array,
    dataLC: Array,
    dataGPS: Array,
    dataEmployees: Array,
    dataPaths: Array,
    selectedDay: String,
  },
  data () {
    return {
      width: 1000,
      height: 800,
      padding: 10,
      margin: {
        left: 155,
        right:10,
        top:20,
        bottom:10
      },
      r: 8,
      namesList: this.getListEmployees(),
    }
  },
  computed: {
    viewBox() {
      return `0 0 ${this.width} ${this.height}`;
    },
    rangeX() {
      const width =this.width - this.padding - this.margin.left - this.margin.right;
      return [0, width];
    },
    rangeY() {
      const height =this.height - this.padding - this.margin.top - this.margin.bottom;
      return [0, height];
    },
  },
  methods: {
    getTimeDomain() {
      var day = new Date(2014,0,parseInt(this.selectedDay));
      var init_time = day.setHours(0, 0, 0, 1);
      var end_time = day.setHours(23, 59, 59, 59);
      return [new Date(init_time), new Date(end_time)]
    },
    scaleX() {
      return d3.scaleTime()
          .domain(this.getTimeDomain())
          .nice()
          .range(this.rangeX);
    },
    valueScaleX(d) {
      const x = this.scaleX();
      return x(d)
    },
    getListEmployees() {
      var namesList = []
      for (let i = 0; i < this.dataEmployees.length; i++) {
        var name = this.dataEmployees[i].fullName;
        namesList.push(name)
      }
      return namesList;
    },
    scaleY() {
      return d3.scalePoint()
          .domain(this.namesList)
          .range(this.rangeY);
      //d3.axisTop(x).ticks(24).tickFormat( d3.utcFormat("%H:%M"));
    },
    valueScaleY(d) {
      const y = this.scaleY();
      return y(d)
    },
    renderAxes() {
      d3.select('.x-axis')
          .call(d3.axisTop(this.scaleX()).ticks(24).tickFormat(d3.timeFormat("%H:%M")))
          .selectAll('.tick line')
          .attr('stroke', 'black')
          .attr('stroke-opacity', '1');

      d3.selectAll('.x-axis path')
          .attr('stroke', 'black')
          .attr('stroke-opacity', '1');

      d3.selectAll(".x-axis .tick text")
          .attr("font-size","13");

      d3.select('.y-axis')
          .call(d3.axisLeft(this.scaleY()).ticks(this.namesList.length) .tickPadding(8))
          .selectAll('.tick line')
          .attr('stroke', 'black' )
          .attr('stroke-opacity', '1');

      d3.selectAll('.y-axis path')
          .attr('stroke', 'black')
          .attr('stroke-opacity','1');

      d3.selectAll(".y-axis .tick text")
          .attr("font-size","15");
    },
    renderYGrid() {
      const yGrid = d3
          .axisLeft(this.scaleY())
          .ticks(this.namesList.length)
          .tickSize(-this.width)
          .tickFormat("");

      d3.select(".y-grid")
          .call(yGrid)
          .selectAll("line")
          .attr("stroke", "black")
          .attr("stroke-opacity", "0.7")
          .attr("stroke-width", "0.8px");

      d3.select(".y-grid path").attr("stroke-opacity", "0");
      d3.select(".x-grid path").attr("stroke-opacity", "0");
    },
    renderXGrid() {
      const xGrid = d3
          .axisTop(this.scaleX())
          .ticks(24)
          .tickSize(-this.height)
          .tickFormat("");

      d3.select(".x-grid")
          .call(xGrid)
          .selectAll("line")
          .attr("stroke", "black")
          .attr("stroke-opacity", "0.2")
          .attr("stroke-width", "0.8px")
          .style("stroke-dasharray", "5 5");

      d3.select(".y-grid path").attr("stroke-opacity", "0");
      d3.select(".x-grid path").attr("stroke-opacity", "0");
    },
    init() {
      this.renderYGrid();
      this.renderXGrid();
      this.renderAxes();
    },
    getInfoCC(cc) {
      console.log(cc.fullName, cc.timestamp, cc.price, cc.location);
    },
    getInfoPath(p) {
      console.log(p.fullName, p.minTimestamp, p.maxTimestamp, p.pathID);
    },
  },
  watch: {
    dataEmployees: function (){
      this.namesList = this.getListEmployees() ;
      this.init();
    }
  },
  mounted() {
    this.namesList = this.getListEmployees() ;
    this.init();
  }
}
</script>

<style scoped>

</style>