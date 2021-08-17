<template>
  <div>
    <svg
        class='timechart'
        transform="translate(0,0)"
        :viewBox="viewBox">
      <g :transform="`translate(0, 0)`">
        <g class="x-axis"
           :transform="`translate(${margin.left }, ${margin.top})`"></g>
        <g class="y-axis"
           :transform="`translate(${margin.left }, ${margin.top})`"></g>

        <g :transform="`translate(${margin.left}, ${margin.top})`">
          <rect
              :transform="`translate(0, 0)`"
              v-for="(cc, i) in dataCC"
              :key="i"
              height="6"
              width="6"
              :x="valueScaleX(cc.timestamp)"
              :y="valueScaleY(cc.fullName)"
              @click="getInfoCC(cc)"></rect>
        </g>

        <g>
          <g class="x-grid grid" :transform="`translate(${margin.left}, ${margin.top})`"></g>
          <g class="y-grid grid" :transform="`translate(${margin.left}, ${margin.top})`"></g>
        </g>

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
    dataEmployees: Array,
  },
  data () {
    return {
      width: 1000,
      height: 1000,
      padding: 10,
      margin: {
        left: 100,
        right:10,
        top:20,
        bottom:10
      },
      namesList: null,
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
    scaleX() {
      const x = d3.scaleTime()
          .domain(this.getTimeDomain())
          .nice()
          .range(this.rangeX);
      return x;
    },
    valueScaleX(d) {
      const x = this.scaleX();
      return x(d)
    },
    getTimeDomain() {
      var day = new Date(this.dataCC[1].timestamp);
      var init_time = day.setHours(0, 0, 0, 1);
      var end_time = day.setHours(23, 59, 59, 59);
      return [new Date(init_time), new Date(end_time)]
    },
    valueScaleY(d) {
      const y = this.scaleY();
      return y(d)
    },
    scaleY() {
      const y = d3.scaleBand()
          .domain(this.getListEmployees())
          .range(this.rangeY);
      //d3.axisTop(x).ticks(24).tickFormat( d3.utcFormat("%H:%M"));
      return y
    },
    getListEmployees() {
      var namesList = []
      for (let i = 0; i < this.dataCC.length; i++) {
        var name = this.dataCC[i].fullName;
        if (namesList.includes(name)) {
          //do nothing
        } else {
          namesList.push(name);
        }
      }
      this.namesList = namesList;
      return namesList;
    },
    getInfoCC(cc) {
      console.log(cc.fullName, cc.timestamp, cc.price, cc.location);
    },
    renderAxes() {
      d3.select('.x-axis')
          .call(d3.axisTop(this.scaleX()).ticks(24).tickFormat(d3.utcFormat("%H:%M")))
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
          .attr("stroke-opacity", "0.2")
          .attr("stroke-width", "1px");

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
          .attr("stroke-width", "1px");

      d3.select(".y-grid path").attr("stroke-opacity", "0");
      d3.select(".x-grid path").attr("stroke-opacity", "0");
    },
    init() {
      this.renderYGrid();
      this.renderXGrid();
      this.renderAxes();
    }
  },
  watch: {
    dataCC:  function (){
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