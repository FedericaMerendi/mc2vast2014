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
              <buttons-day @get-day="getSelectedDay"/>
            </div>
          </b-row>

          <!-- Events timeline -->
          <b-row>
            <div class="eventTimeline">
              <h5> Daily employees timeline</h5>
              <event-timeline :data-c-c="dataCC"
                              :data-l-c="dataLC"
                              :data-paths="dataPaths"
                              @update-time="updateTime"
                              @display-path="displayPath"
                              @update-expenses="getLocationExpenses"
                              @update-loyalty-expenses="getLocationLoyalty"
                              @get-location="getLocation"/>
            </div>
          </b-row>

        </b-col>
        <b-col cols="7" class="right_viz">
          <!-- Employees treemap -->
          <b-row>
            <div class="treemap">
              <h5> TreeMap </h5>
              <tree-map
                  @get-employee="filterEmployee"
                  @get-title="filterTitle"
                  @get-type="filterType"/>
            </div>
          </b-row>

          <b-row>
            <b-col cols="7">
              <!-- Map -->
              <div class="map">
                <h5> Map </h5>
                <abila-map :dataGPS="dataGPS"
                          :locations="locations"/>
              </div>
            </b-col>

            <b-col cols="5">
              <!-- Expenses Analysis  -->
             <b-row>
                <div class="expensesAnalysis" >
                  <p> Spesa individuale vs media location sua e di altri </p>
                  <expenses-chart :expenses="expensesCC"
                                  :categories="categoriesCC"/>
                </div>
              </b-row>

             <b-row>
                <div class="expensesAnalysis">
                  <p> LC individuale vs spese totali sue e di altri </p>
                  <expenses-chart :expenses="expensesLC"
                                 :categories="categoriesLC"/>
                </div>

              </b-row>
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
let byEmpGPS2;
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
      expensesCC: [],
      categoriesCC: [],
      expensesLC: [],
      categoriesLC: [],
      selectedDay: '6',
      selectedLocation: null,
      selectedEmployee: null,
      selectedType: null,
      selectedTitle:null,
      selectedDateRange: this.rangeDate,
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
    getLocation(location) {
      if (location !== 'Driving' && location !== 'Loyalty Card') {
        this.selectedLocation = location;

        this.filterChartPerEmployee(this.selectedEmployee, this.selectedLocation);
        this.filterChartPerTitleType(this.selectedType, byEmpType, this.selectedLocation);
        this.filterChartPerTitleType(this.selectedTitle, byEmpTitle, this.selectedLocation);
      }
    },
    sublistName(array) {
      /*  given an array it generates a list of names */
      let names = []
      for (let i =0 ;i < array.length; i++) {
        names.push(array[i].fullName)
      }
      return names
    },

    resetFiltersEmployees() {
      /*It resets all the filters related to the employees data */
      byEmpName.filterAll();
      byEmpTitle.filterAll();
      byEmpType.filterAll()
      byEmpGPS1.filterAll();
      byEmpGPS2.filterAll();
      byPathGPS1.filterAll();
      byPathGPS2.filterAll();

    },
    filterMapPerEmployee(name){
      /* given an employee name it displays only his/her paths on the map */
      let day = this.selectedDay;
      this.resetFiltersEmployees();

      byEmpName.filterExact(name);
      byEmpGPS2.filterExact(name);
      byEmpGPS1.filterExact(name);

      if (day === '6' || day === '7' || day === '8' ||
          day === '9' || day === '10' || day === '11' ||day === '12') {
        this.dataGPS = byEmpGPS1.top(Infinity);
      } else {
        this.dataGPS = byEmpGPS2.top(Infinity);
      }
    },

    filterMapPerTitle(title){
      /* given an employee title it displays only its members paths on the map */
      let day = this.selectedDay;
      this.resetFiltersEmployees()

      let titleEmp = byEmpTitle.filter(title).top(Infinity);
      let names = this.sublistName(titleEmp);

      byEmpGPS1.filter(function (d) {
        return names.indexOf(d) > -1;
      });
      byEmpGPS2.filter(function (d) {
        return names.indexOf(d) > -1;
      });

      if (day === '6' || day === '7' || day === '8' ||
          day === '9' || day === '10' || day === '11' ||day === '12') {
        console.log(byEmpGPS1.top(Infinity))
        this.dataGPS = byEmpGPS1.top(Infinity);
      } else {
        this.dataGPS = byEmpGPS2.top(Infinity);
      }
    },

    filterMapPerType(type) {
      /* given an employee type it displays only its members paths on the map */
      let day = this.selectedDay;
      this.resetFiltersEmployees();

      let typeEmp = byEmpType.filter(type).top(Infinity);
      let names = this.sublistName(typeEmp);

      byEmpGPS1.filter(function (d) {
        return names.indexOf(d) > -1;
      });
      byEmpGPS2.filter(function (d) {
        return names.indexOf(d) > -1;
      });

      if (day === '6' || day === '7' || day === '8' ||
          day === '9' || day === '10' || day === '11' ||day === '12') {
        if(type === undefined) {
          this.dataGPS = byEmpGPS1.filterAll().top(Infinity);
        } else {
          this.dataGPS = byEmpGPS1.top(Infinity);
        }
      } else {
        if(type === undefined) {
          this.dataGPS = byEmpGPS2.filterAll().top(Infinity);
        } else {
          this.dataGPS = byEmpGPS2.top(Infinity);
        }
      }
    },

    filterChartPerEmployee(name, location) {
      /* given an employeee it creates the chart related to the CC expenses and the loyalty card*/
      this.resetFiltersEmployees();
      byDateCC.filterRange(this.selectedDateRange).top(Infinity);
      byDateLC.filterRange(this.selectedDateRange).top(Infinity);

      if (location !== null){
        byLocationCC.filterExact(location).top(Infinity);
        byLocationLC.filterExact(location).top(Infinity);
        this.categoriesCC = [['Average', 'expenses of', name, 'at', location],
          ['Average', 'expenses of','the other' ,'employees at', location]];
        this.categoriesLC = [['Average', 'loyalty card', 'value for', name, 'at', location],
          ['Average', 'loyalty card', 'value for','the other','employees at', location]];
      } else {
        this.categoriesCC = [['Average', 'expenses of', name],
          ['Average', 'expenses of','the other employees']];
        this.categoriesLC = [['Average', 'loyalty card', 'value for', name],
          ['Average', 'loyalty card', 'value for','the other employees']];
      }


      let allCC = this.avgPrice(byNameCC.filterFunction(function(d) { return d !== name; }).top(Infinity));
      let empCC = this.avgPrice(byNameCC.filterExact(name).top(Infinity));
      this.expensesCC = [empCC, allCC];

      let allLC = this.avgPrice(byNameLC.filterFunction(function(d) { return d !== name; }).top(Infinity));
      let empLC = this.avgPrice(byNameLC.filterExact(name).top(Infinity));
      this.expensesLC = [empLC, allLC];
      byNameCC.filterAll();
      byNameLC.filterAll();
      byLocationCC.filterAll();
      byLocationLC.filterAll();
    },

    filterChartPerTitleType(secName, d , location) {
      /*given a subset of employees it creates the charts related to the CC expenses and the Loyalty Card*/
      this.resetFiltersEmployees();
      byDateCC.filterRange(this.selectedDateRange).top(Infinity);
      byDateLC.filterRange(this.selectedDateRange).top(Infinity);

      if (location !== null){
        byLocationCC.filterExact(location).top(Infinity);
        byLocationLC.filterExact(location).top(Infinity);
      }

      if (secName === undefined) {
        let allCC = this.avgPrice(byNameCC.filterAll().top(Infinity));
        this.categoriesCC = ['Average expenses of all the employees']
        this.expensesCC = [allCC]

        let allLC = this.avgPrice(byNameLC.filterAll().top(Infinity));
        this.categoriesLC = ['Average loyalty card value of all the employees']
        this.expensesLC = [allLC]

      } else {
        let emp = d.filter(secName).top(Infinity);
        let names = this.sublistName(emp);

        //let categories = [empData.price]
        byNameCC.filterAll();
        byNameLC.filterAll();

        let othersCC = this.avgPrice(byNameCC.filterFunction(function (d) {
          return names.indexOf(d) === -1;
        }).top(Infinity));
        let sectionCC = this.avgPrice(byNameCC.filterFunction(function (d) {
          return names.indexOf(d) > -1;
        }).top(Infinity));
        this.categoriesCC = [['Average', 'expenses of ', secName, 'employees'],
          ['Average', 'expenses of', 'the other employees']]
        this.expensesCC = [sectionCC, othersCC];

        let othersLC = this.avgPrice(byNameLC.filterFunction(function (d) {
          return names.indexOf(d) === -1;
        }).top(Infinity));

        let sectionLC = this.avgPrice(byNameLC.filterFunction(function (d) {
          return names.indexOf(d) > -1;
        }).top(Infinity));

        this.categoriesLC = [['Average', 'loyalty card ', 'value of', secName, 'employees'],
          ['Average','loyalty card ', 'value of the', 'other employees']]
        this.expensesLC = [sectionLC, othersLC];
      }
      console.log(this.expensesCC, this.expensesLC);
      byNameCC.filterAll();
      byNameLC.filterAll();
      byLocationCC.filterAll();
      byLocationLC.filterAll();
    },

    filterEmployee(name) {
      /*given an employee it updates the map and the chart with his/her data only */
      console.log('filter by',name);
      this.selectedName = name;
      this.selectedType = null;
      this.selectedTitle = null;
      this.filterMapPerEmployee(name);
      this.filterChartPerEmployee(name, this.selectedLocation);
      },

    filterTitle(title){
      console.log('filter by',title);
      this.selectedName = null;
      this.selectedType = null;
      this.selectedTitle = title;
      this.filterMapPerTitle(title);
      this.filterChartPerTitleType(title, byEmpTitle, this.selectedLocation);
    },

    filterType(type){
      console.log('filter by',type);
      this.selectedName = null;
      this.selectedType = type;
      this.selectedTitle = null;
      this.filterMapPerType(type);
      this.filterChartPerTitleType(type, byEmpType, this.selectedLocation);
    },

    avgPrice(array) {
      /*calculates the average price of an array of data */
      console.log(array)
      let sum = 0
      for (let i = 0; i < array.length; i++) {
        sum = sum + array[i].price;
      }
      return Math.round(( (sum / array.length)+ Number.EPSILON) * 100) / 100
    },


    getLocationExpenses(empData) {
      /* Given a payment it aggregates the data based on the current selected payment,
     * the average for the location at the employee, and the average at the location for all the employees */

      console.log(empData.fullName, empData.timestamp, empData.price, empData.location)

      //let categories = [empData.price]
      byDateCC.filterAll().top(Infinity);
      byLocationCC.filterExact(empData.location).top(Infinity);
      let allLoc = this.avgPrice(byNameCC.filterFunction(function(d) { return d !== empData.fullName; }).top(Infinity));
      let empLoc = this.avgPrice(byNameCC.filterExact(empData.fullName).top(Infinity));
      this.categoriesCC = [['Paid price to', empData.location, 'at', empData.timestamp.toLocaleTimeString()],
                                ['Average', 'expenses of', empData.fullName, 'at ' , empData.location ],
                                ['Average', 'expenses of','the other employees', 'at ', empData.location ]]
      this.expensesCC = [empData.price, empLoc, allLoc];

      console.log(this.expensesCC)
      byNameCC.filterAll()
      byLocationCC.filterAll()
    },

    getLocationLoyalty(empData) {
      /* Given a loyalty card it aggregates the data based on the current selected payment,
      * the average for the location at the employee, and the average at the location for all the employees */
      console.log(empData.fullName, empData.timestamp, empData.price, empData.location)

      byDateLC.filterAll().top(Infinity);
      byLocationLC.filterExact(empData.location).top(Infinity);
      let allLoc = this.avgPrice(byNameLC.filterFunction(function(d) { return d !== empData.fullName; }).top(Infinity));
      let empLoc = this.avgPrice(byNameLC.filterExact(empData.fullName).top(Infinity));
      this.categoriesLC = [['Loyalty card from', empData.location, 'used at', empData.timestamp.toLocaleTimeString()],
                                ['Average', 'loyalty card','value for', empData.fullName, 'at ' , empData.location ],
                                ['Average', 'loyalty card', 'value for the','other employees', 'at ', empData.location ]]
      this.expensesLC = [empData.price, empLoc, allLoc];
      console.log(this.expensesLC)
      byNameLC.filterAll()
      byLocationLC.filterAll()
    },

    displayPath(pathID) {
      /* Given the ID of the selected path it filters the GPS data to display only that path */
      this.reset = true;
      console.log(pathID)
      byEmpGPS1.filterAll();
      byEmpGPS2.filterAll();

      let day = this.selectedDay;
      if (day === '6' || day === '7' || day === '8' ||
          day === '9' || day === '10' || day === '11' ||day === '12') {
        byDateGPS1.filterAll()
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
      this.selectedDateRange = [min, max];

      if (min.getDate() === 6 || min.getDate() === 7 ||min.getDate() === 8
          ||min.getDate() === 9 ||min.getDate() === 10 || min.getDate() === 11 ||min.getDate() === 12) {
        byPathGPS1.filterAll()
        this.dataGPS = byDateGPS1.filterRange([min,max]).top(Infinity);
      } else {
        byPathGPS2.filterAll()
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
      if (day === '6' || day === '7' || day === '8' ||
          day === '9' || day === '10' || day === '11' ||day === '12') {
        byPathGPS1.filterAll()
        this.dataGPS = byDateGPS1.filterRange(range).top(Infinity);
      } else {
        byPathGPS2.filterAll()
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
          byEmpTitle = cfEmp.dimension((d) =>{ return d.currentEmpTitle});
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
          this.locations = location;
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
          byEmpGPS2 = cfGPS2.dimension((d) => d.fullName);
          byPathGPS2 = cfGPS2.dimension((d) => d.pathID);
          byEmpGPS2.filterAll();
          byPathGPS2.filterAll();
          byDateGPS2 = cfGPS2.dimension((d) => d.timestamp);
          this.dataGPS = byDateGPS2.filterRange(this.rangeDate).top(Infinity);
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
          byEmpGPS1 = cfGPS1.dimension((d) => d.fullName);
          byPathGPS1 = cfGPS1.dimension((d) => d.pathID);
          byPathGPS1.filterAll();
          byEmpGPS1.filterAll();
          byDateGPS1 = cfGPS1.dimension((d) => d.timestamp);
          this.dataGPS = byDateGPS1.filterRange(this.rangeDate).top(Infinity);
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