<template>
  <div>
    <h2> GAStech employees analysis </h2>
    <b-container fluid>
      <b-row id="firstRow" >
        <b-col cols="5" id="userData">
          <!-- Buttons to select the day -->
          <b-row>
            <div class="daySelection">
              <h5> Select a day </h5>
              <ButtonsDay @get-day="getSelectedDay"/>
            </div>
          </b-row>

          <!-- Events timeline -->
          <b-row>
            <div class="eventTimeline">
              <h5> Daily employees timeline</h5>
              <EventTimeline :dataCC="dataCC"
                             :dataLC="dataLC"
                             :dataPaths="dataPaths"
                             @update-time="updateTime"
                             @display-path="displayPath"
                             @update-expenses="getLocationExpenses"
                             @update-loyalty-expenses="getLocationLoyalty"/>
            </div>
          </b-row>

        </b-col>
        <b-col cols="7" class="right_viz">
          <!-- Employees treemap -->
          <b-row>
            <div class="treemap">
              <h5> TreeMap </h5>
              <TreeMap
                  @get-employee="filterEmployee"
                  @get-title="filterTitle"
                  @get-type="filterType"/>
            </div>
          </b-row>

          <b-row>
            <b-col cols="8">
              <!-- Map -->
              <div class="map">
                <h5> Map </h5>
                <AbilaMap :dataGPS="dataGPS"
                          :locations="locations"/>
              </div>
            </b-col>

            <b-col cols="4">
              <!-- Expenses Analysis  -->
             <b-row>
                <div class="expensesAnalysis" >
                  <p> Spesa individuale vs media location sua e di altri </p>
                  <ExpensesChart :expenses="locationExpenses"
                                  :categories="locationCategories"/>
                </div>
              </b-row>

             <b-row>
                <div class="expensesAnalysis">
                  <p> LC individuale vs spese totali sue e di altri </p>
                  <ExpensesChart :expenses="loyaltyExpenses"
                                 :categories="loyaltyCategories"/>
                </div>

              </b-row>-->
            </b-col>

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
import ExpensesChart from "./ExpensesChart";

const d3 = require('d3');
import crossfilter from 'crossfilter2';

let byDateCC;
let byNameCC;
let byLocationCC;

let byDateLC;
let byNameLC;
let byLocationLC;

let byDatePaths;

let byEmpGPS1;
let byDateGPS1;
let byPathGPS1;
let byDateGPS2;
let byPathGPS2;

let byEmpType;
let byEmpTitle;
let byEmpName;

export default {
  name: "MainLayout",
  components: {
    EventTimeline,
    ExpensesChart,
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
      locationExpenses: [],
      locationCategories: [],
      loyaltyExpenses: [],
      loyaltyCategories: [],
      selectedDay: 6,
    }
  },
  computed: {
    rangeDate() {
      /* It returns the time range of the selected day */
      let init_time = new Date(2014,0,this.selectedDay,0, 0, 0, 0);
      let end_time = new Date(2014,0,this.selectedDay,23, 59, 59, 59);

      console.log(this.selectedDay,init_time, end_time)
      return [init_time, end_time]
    },

  },
  methods: {
    filterEmployee(name){
      console.log('filter by',name);
      byEmpGPS1.filter(name);
      byEmpName.filter(name).top(Infinity);
      this.dataGPS = byEmpGPS1.top(Infinity);

    },

    filterTitle(title){
      byEmpTitle.filter(title).top(Infinity);
      console.log('filter by',title);
    },

    filterType(type){
      byEmpType.filter(type).top(Infinity);
      console.log('filter by',type);
    },

    avgPrice(array) {
      let sum = 0
      for (let i = 0; i < array.length; i++) {
        sum = sum + array[i].price;
      }
      return Math.round(( (sum / array.length)+ Number.EPSILON) * 100) / 100
    },

    getLocationExpenses(empData) {
      console.log(empData.fullName, empData.timestamp, empData.price, empData.location)

      //let categories = [empData.price]
      byDateCC.filterAll().top(Infinity);
      byLocationCC.filterExact(empData.location).top(Infinity);
      let allLoc = this.avgPrice(byNameCC.filterAll().top(Infinity));
      let empLoc = this.avgPrice(byNameCC.filterExact(empData.fullName).top(Infinity));
      this.locationCategories = ['Expense', 'Average\n employee\n expenses', 'Average expenses  of all employees']
      this.locationExpenses = [empData.price, empLoc, allLoc];
      console.log(this.locationExpenses)
      byNameCC.filterAll()
      byLocationCC.filterAll()
    },

    getLocationLoyalty(empData) {
      console.log(empData.fullName, empData.timestamp, empData.price, empData.location)

      byDateLC.filterAll().top(Infinity);
      byLocationLC.filterExact(empData.location).top(Infinity);
      let allLoc = this.avgPrice(byNameLC.filterAll().top(Infinity));
      let empLoc = this.avgPrice(byNameLC.filterExact(empData.fullName).top(Infinity));
      this.loyaltyCategories = ['Expense', 'Average\n employee\n expenses', 'Average expenses  of all employees']
      this.loyaltyExpenses = [empData.price, empLoc, allLoc];
      console.log(this.locationExpenses)
      byNameLC.filterAll()
      byLocationLC.filterAll()
    },

    displayPath(pathID) {
      /* Given the ID of the selected path it filters the GPS data to display only that path */
      let day = this.selectedDay;
      if (day === 6 || day === 7 || day === 8 ||
          day === 9 || day === 10 || day === 11 ||day === 12) {
        this.dataGPS = byPathGPS1.filterExact(pathID).top(Infinity);
      } else {
        byDateGPS2.filterAll()
        this.dataGPS = byPathGPS2.filterExact(pathID).top(Infinity);
      }

    },
    updateTime(dates) {
      /*Given a range of dates it updates the GPS data to display the current paths*/
      let min, max;
      if (dates[0] == null && dates[1] == null){
         let range = this.rangeDate
         min = range[0];
         max = range[1];
      } else {
        min = new Date(dates[0]);
        max = new Date(dates[1]);
      }
      console.log('time range update', min, max)

      if (min.getDate() === 6 || min.getDate() === 7 ||min.getDate() === 8
          ||min.getDate() === 9 ||min.getDate() === 10 || min.getDate() === 11 ||min.getDate() === 12) {
        byPathGPS1.filterAll()
        this.dataGPS = byDateGPS1.filterRange([min,max]).top(Infinity);
      } else {
        byPathGPS1.filterAll()
        this.dataGPS = byDateGPS2.filterRange([min,max]).top(Infinity);
      }
    },
    getSelectedDay(day) {
      /* given a day it filters all the visualization */
      console.log('Selected day:', day);
      this.selectedDay = day;
      let range = this.rangeDate;
      //console.log('app',dCCDay.filter(this.selectedDay).top(Infinity));
      this.dataCC = byDateCC.filterRange(range).top(Infinity);
      this.dataPaths = byDatePaths.filterRange(range).top(Infinity);
      this.dataLC = byDateLC.filterRange(range).top(Infinity);

      if (day === 6 || day === 7 || day === 8 ||
          day === 9 || day === 10 || day === 11 ||day === 12) {
        byPathGPS1.filterAll()
        this.dataGPS = byDateGPS1.filterRange(range).top(Infinity);
      } else {
        byPathGPS1.filterAll()
        this.dataGPS = byDateGPS2.filterRange(range).top(Infinity);
      }

    },
  },
  mounted() {
    /* import of the employees data */
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

          /* creating the dimension to filter the employees dataset */
          let cfEmp = crossfilter(employee);
          byEmpType = cfEmp.dimension((d) =>{ return d.currentEmpType});
          byEmpTitle = cfEmp.dimension((d) =>{ return d.currentEmpType});
          byEmpName = cfEmp.dimension((d) =>{ return d.fullName});

          this.dataEmployees = byEmpType.top(Infinity);
        });


    /* import of the loyalty card data */
    d3.csv('/data/loyalty-fullname-time.csv')
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
          byDateLC = cfLC.dimension(d => {return d.timestamp});
          byNameLC = cfLC.dimension(d => {return d.fullName});
          byLocationLC = cfLC.dimension(d => {return d.location});

          this.dataLC = byDateLC.filterRange(this.rangeDate).top(Infinity);
        });


    /* import of the credit cards data */
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
          byDateCC = cfCC.dimension(d => { return d.timestamp});
          byNameCC = cfCC.dimension(d => {return d.fullName});
          byLocationCC = cfCC.dimension(d => {return d.location});

          this.dataCC = byDateCC.filterRange(this.rangeDate).top(Infinity);
        });


    /* import of the paths data */
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
          byDatePaths = cfPaths.dimension(d => { return d.minTimestamp});
          this.dataPaths = byDatePaths.filterRange(this.rangeDate).top(Infinity);
        });


    /* import of the GPS data, first part */
    d3.csv('/data/gps-fullname-6-12.csv')
        .then((rows) => {
          const gps1 = rows
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

          let cfGPS1 = crossfilter(gps1);
          //byEmpGPS1 = cfGPS1.dimension((d) => d.fullName);
          byPathGPS1 = cfGPS1.dimension((d) => d.pathID);
          byPathGPS1.filterAll()
          byDateGPS1 = cfGPS1.dimension((d) => d.timestamp);
          this.dataGPS = byDateGPS1.filterRange(this.rangeDate).top(Infinity);
        });


    /* import of the GPS data, second part */
    d3.csv('/data/gps-fullname-13-19.csv')
        .then((rows) => {
          const gps2 = rows
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

          let cfGPS2 = crossfilter(gps2);
          //byEmpGPS2 = cfGPS.dimension((d) => d.fullName);
          byPathGPS2 = cfGPS2.dimension((d) => d.pathID);
          byPathGPS2.filterAll()
          byDateGPS2 = cfGPS2.dimension((d) => d.timestamp);
          this.dataGPS = byDateGPS2.filterRange(this.rangeDate).top(Infinity);
        });


    /* import of the locations data */
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
          this.location = location;
        });

  },
}

</script>

<style scoped>
.expensesAnalysis {
  width: 100%;
  height: 250px;
  margin: 10px 2px 5px 2px;
  padding: 5px;
}
</style>