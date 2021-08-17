<template>
  <div>
    <p> Hello There! </p>
    <b-container fluid>
      <b-row id="firstRow" >
        <b-col cols="7" id="userData">
          <ButtonsDay @get-day="getSelectedDay" ></ButtonsDay>
          <!-- <Employee v-bind:data="data"
                    v-bind:encoding="encoding"
                    @get-employee-name="getName"/>-->

          <Employee :dataEmployees="dataEmployees"
                    @get-employee-name="getName"/>
        </b-col>
        <b-col cols="5" class="right_viz">
          <b-row>
            <div class="map">
              <h3> Map </h3>
              <AbilaMap :dataGPS="dataGPS"/>
            </div>
          </b-row>
          <b-row>
            <div class="filters">
              <h3> Filters </h3>
            </div>
          </b-row>
        </b-col>

      </b-row>
    </b-container>
  </div>
</template>

<script>

import ButtonsDay from "./ButtonsDay";
const d3 = require('d3');
import crossfilter from 'crossfilter2';
import AbilaMap from "./AbilaMap";
import Employee from "./Employee";

let employee;
let loyalty_cards;

let credit_cards;
let dCCDay;

let dLocationLC;

let dEmployeeID;

var dGPSCarID;
var dGPSDay;

//let dSubject;

export default {
  name: "MainLayout",
  components: {
    ButtonsDay,
    AbilaMap,
    Employee,
  },
  data(){
    return {
      dataEmployees: [],
      loyalty_cards: [],
      credit_cards: [],
      dataGPS: [],
      selectedDay: '6',
      encoding: {
        x: {field:'timestamp', type: 'temporal'},
        y: {field:'lastName', type: 'nominal'},
        size: {field:'price', type:'quantitative'},
        color: {field: "location", type: "nominal"},
      },
    }
  },
  methods: {
    getSelectedDay(day) {
      console.log('Selected day:', day);
      this.selectedDay = day;
      //console.log('app',dCCDay.filter(this.selectedDay).top(Infinity));

      this.credit_cards = dCCDay.filter(this.selectedDay).top(Infinity);
      this.dataGPS = dGPSDay.filter(this.selectedDay).top(Infinity);
      //this.loyalty_card = dLCDay.filter(this.selectedDay).top(Infinity);
    },
    getCarID(id){
      console.log('car', id)
      dGPSCarID.filter(id.toString());
      this.dataGPS = dGPSCarID.top(Infinity);
    },
    getName(id){
      dEmployeeID.filter(id);
      var personCarID = dEmployeeID.top(Infinity)[0]['carID'];
      this.getCarID(personCarID);
    },
  },
  mounted() {
    d3.csv('/data/car-assignments-ids.csv')
        .then((rows) => {
          employee = rows
              .map((row) => {
                return {
                  id: row.EmployeeID,
                  lastName: row.LastName,
                  firstName: row.FirstName,
                  carID: +row.CarID,
                  currentEmploymentType: row.CurrentEmploymentType,
                  currentEmploymentTitle: row.CurrentEmploymentTitle
                };
              });
          let cfEmp = crossfilter(employee);
          //dEmployment = cfEmp.dimension((d) =>{ return d.currentEmploymentType});
          //this.dEmployment = dEmployment;

          dEmployeeID = cfEmp.dimension((d) => {return d.id});

          //console.log(dName.filter('Hennie Osvaldo').top(5))
          this.dataEmployees = dEmployeeID.top(Infinity);
        });

    d3.csv('/data/loyalty_data.csv')
        .then((rows) => {
          loyalty_cards = rows
              .map((row) => {
                return {
                  day: new Date(row.timestamp),
                  location: row.location,
                  price: +row.price,
                  firstName: row.FirstName,
                  lastName: row.LastName,
                };
              });
          let cfLC = crossfilter(loyalty_cards);
          dLocationLC = cfLC.dimension((d) => {return d.location;});
          // console.log(dLocationLC.group().all());

          this.dLocationLC = dLocationLC;
          this.loyalty_cards = loyalty_cards;
        });

    d3.csv('/data/cc_data.csv')
        .then((rows) => {
          credit_cards = rows
              .map((row) => {
                return {
                  timestamp: new Date(row.timestamp),
                  location: row.location,
                  price: +row.price,
                  firstName: row.FirstName,
                  lastName: row.LastName,
                };
              });
          let cfCC = crossfilter(credit_cards);
          dCCDay = cfCC.dimension(d => { return d.timestamp.getDate()});
          this.credit_cards = dCCDay.filter(this.selectedDay).top(Infinity);

        });

    d3.csv('/data/reduced_gps.csv')
        .then((rows) => {
          const gps = rows
              .map((row) => {
                return {
                  timestamp: new Date(row.Timestamp),
                  carID: +row.id,
                  lat: row.lat,
                  long: row.long,
                };
              });
          let cfGPS = crossfilter(gps);
          dGPSCarID = cfGPS.dimension((d) => d.carID);
          dGPSDay = cfGPS.dimension((d) => d.timestamp.getDate());
          //var dGPSTime = cfGPS.dimension((d) => d.timestamp.getTime());
          //console.log(dGPSDay.top(Infinity));
          //console.log(dGPSTime.top(Infinity));
          this.dataGPS = dGPSCarID.filter(this.selectedDay).top(Infinity);
        });
  },
}

</script>

<style scoped>
.filters, .map, #userData {
  background-color: beige;
 }
</style>