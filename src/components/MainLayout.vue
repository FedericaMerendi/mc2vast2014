<template>
  <div>
    <h2> GAStech employees analysis </h2>
    <b-container fluid>
      <b-row id="firstRow" >
        <b-col cols="7" id="userData">
          <h5> Select a day </h5>
          <ButtonsDay @get-day="getSelectedDay" ></ButtonsDay>
          <h5> Daily employees timeline</h5>
          <EventTimeline :dataCC="dataCC"
                         :dataEmployees="dataEmployees"
                         :dataLC="dataLC"
                         :dataPaths="dataPaths"
                         :selectedDay="selectedDay"
          @update-time="updateTime"></EventTimeline>

        </b-col>
        <b-col cols="5" class="right_viz">
          <b-row>
            <div class="treemap">
              <h5> TreeMap </h5>
              <TreeMap></TreeMap>
            </div>
          </b-row>
          <b-row>
            <div class="map">
              <h5> Map </h5>
              <AbilaMap :dataGPS="dataGPS"
                        :locations="locations"/>
            </div>
          </b-row>
        </b-col>

      </b-row>
    </b-container>
  </div>
</template>

<script>
import AbilaMap from "./AbilaMap";
import TreeMap from "./TreeMap";
import ButtonsDay from "./ButtonsDay";
import EventTimeline from "./EventTimeline";

const d3 = require('d3');
import crossfilter from 'crossfilter2';


let dCCDay;
let dEmpID;
let dGPSCarID;
let dGPSDay;
let dLCDay;
let dPathsDay;
let dGPSPathID;

//let dSubject;

export default {
  name: "MainLayout",
  components: {
    EventTimeline,
    ButtonsDay,
    AbilaMap,
    TreeMap,
  },
  data(){
    return {
      dataEmployees: [],
      dataLC: [],
      dataCC: [],
      dataGPS: [],
      dataPaths: [],
      locations: [],
      selectedDay: '6',
    }
  },
  methods: {
    updateTime(min) {
      console.log('hello there', min)
    },
    getSelectedDay(day) {
      console.log('Selected day:', day);
      this.selectedDay = day;
      //console.log('app',dCCDay.filter(this.selectedDay).top(Infinity));

      this.dataCC = dCCDay.filter(this.selectedDay).top(Infinity);
      this.dataGPS = dGPSDay.filter(this.selectedDay).top(Infinity);
      this.dataPaths = dPathsDay.filter(this.selectedDay).top(Infinity);

      //this.loyalty_card = dLCDay.filter(this.selectedDay).top(Infinity);
    },
    getCarID(id){
      console.log('car', id)
      dGPSCarID.filter(id.toString());
      this.dataGPS = dGPSCarID.top(Infinity);
    },
    getName(id){
      dEmpID.filter(id);
      let personCarID = dEmpID.top(Infinity)[0]['carID'];
      this.getCarID(personCarID);
    },
    getUTCTime(timestamp) {
      var date = new Date(timestamp)
      var utcdate = Date.UTC(date.getFullYear(),date.getMonth(),date.getDay(), date.getHours(),date.getMinutes(), date.getSeconds());
      console.log(utcdate, date) ;
      return utcdate
    },
  },
  mounted() {
    d3.csv('/data/employees-fullname.csv')
        .then((rows) => {
          const employee = rows
              .map((row) => {
                return {
                  fullName: row.FullName,
                  carID: +row.CarID,
                  currentEmpType: row.CurrentEmploymentType,
                  currentEmpTitle: row.CurrentEmploymentTitle
                };
              });
          //let cfEmp = crossfilter(employee);
          //dEmployment = cfEmp.dimension((d) =>{ return d.currentEmploymentType});
          //this.dEmployment = dEmployment;

          //dEmpID = cfEmp.dimension((d) => {return d.employeeID});

          //console.log(dName.filter('Hennie Osvaldo').top(5))
          this.dataEmployees = employee //dEmpID.top(Infinity);
        });

    d3.csv('/data/loyalty-data-fullname.csv')
        .then((rows) => {
          const loyalty_cards = rows
              .map((row) => {
                return {
                  timestamp: new Date(row.timestamp),
                  location: row.location,
                  price: +row.price,
                  fullName: row.FullName,
                };
              });
          let cfLC = crossfilter(loyalty_cards);
          //dLocationLC = cfLC.dimension((d) => {return d.location;});
          // console.log(dLocationLC.group().all());
          dLCDay = cfLC.dimension(d => { return d.timestamp.getDate()});

          this.dataLC = dLCDay.filter(this.selectedDay).top(Infinity);
        });

    d3.csv('/data/cc-data-fullname.csv')
        .then((rows) => {
          const credit_cards = rows
              .map((row) => {
                return {
                  timestamp: new Date(row.timestamp),
                  location: row.location,
                  price: +row.price,
                  fullName: row.FullName,
                };
              });
          let cfCC = crossfilter(credit_cards);
          dCCDay = cfCC.dimension(d => { return d.timestamp.getDate()});

          this.dataCC = dCCDay.filter(this.selectedDay).top(Infinity);

        });

    d3.csv('/data/gps-fullname.csv')
        .then((rows) => {
          const gps = rows
              .map((row) => {
                return {
                  timestamp: new Date(row.Timestamp),
                  carID: +row.CarID,
                  lat: row.lat,
                  long: row.long,
                  fullName: row.FullName,
                  pathID: +row.pathID,
                };
              });

          let cfGPS = crossfilter(gps);
          dGPSCarID = cfGPS.dimension((d) => d.carID);
          dGPSDay = cfGPS.dimension((d) => d.timestamp.getDate());
          dGPSPathID = cfGPS.dimension((d) => d.pathID);
          //var dGPSTime = cfGPS.dimension((d) => d.timestamp.getTime());
          //console.log(dGPSDay.top(Infinity));
          //console.log(dGPSTime.top(Infinity));
          dGPSPathID.filterAll();
          dGPSDay.filter(this.selectedDay).top(Infinity);
          //console.log(dGPSCarID.filter('35').top(Infinity));
          //console.log(dGPSPathID.group().top(Infinity));

          this.dataGPS = dGPSDay.filter(this.selectedDay).top(Infinity);
        });

    d3.csv('/data/paths_united.csv')
        .then((rows) => {
          const paths = rows
              .map((row) => {
                return {
                  fullName: row.FullName,
                  carID: +row.CarID,
                  pathID: +row.pathID,
                  minTimestamp: new Date(row.min_timestamp),
                  minLat: +row.min_lat,
                  minLong: +row.min_long,
                  maxLat: +row.max_lat,
                  maxLong: +row.max_long,
                  maxTimestamp: new Date(row.max_timestamp),
                };
              });
          let cfPaths= crossfilter(paths);
          //console.log(paths)
          dPathsDay = cfPaths.dimension(d => { return d.minTimestamp.getDate()});
          this.dataPaths = dPathsDay.filter(this.selectedDay).top(Infinity);
        });

    d3.csv('/data/locations.csv')
        .then((rows) => {
          const location = rows
              .map((row) => {
                return {
                  location: row.location,
                  lat: +row.lat,
                  long: +row.long,
                };
              });
          this.locations = location;
        });

  },
}

</script>

<style scoped>
</style>