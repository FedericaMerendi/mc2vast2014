<template>
  <div>
    <h3> GAStech International employees analysis </h3>
    <p> GAStech International has been operating a natural gas production site in the island country of Kronos, in particular the city of Abila.
      In January 2014 several employee go missing. This visualization has the aim to help identify suspicious behaviors.</p>
    <b-container fluid>
      <b-row id="firstRow" >
        <b-col cols="5" id="userData">
          <!-- Buttons to select the day -->
          <b-row>
            <div class="daySelection">
              <h5>1. Select a day </h5>
              <p>The available data are two weeks before the disappearance of the employees.
                <span class="subtext">Select a day to analyze it:</span></p>
              <buttons-day @get-day="getSelectedDay"/>
            </div>
          </b-row>

          <!-- Events timeline -->
          <b-row>
            <div class="eventTimeline">
              <h5> 3. Analyze the employees daily timeline</h5>
              <p> The timeline shows the daily schedule of each employee based on the GPS, the Credit Card and the Loyalty Card data.
              <br> <span class="subtext"> The time zoom permits to highlight a certain time of the day.
                  <br> It is also possible to click on the data point:
                  if the data refers to the GPS it will show the path on the map;
                  while if it refers to Credit Cards or Loyalty Card transactions it will show an insight of the expense in the barchart.
                  Finally, the legend of the locations let you filter the barchart in conjunction to the company's structure TreeMap.
                  To reset the timeline, click on the house icon at the top-right on the visualization.
                  </span></p>

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
              <h5> 2. Company structure </h5>
              <p id="treemap-p">GAStech International has 5 different branches, each employee has an employment type and an employment title. 35 of the employees use a company car.
              <br><span class="subtext"> Select an employment type, title or a employee to show in the map and in the bar charts the relative subset of data.
                To reset the chart, click on the main category 'All'. </span></p>
              <tree-map id="tmComponent"
                  @get-employee="filterEmployee"
                  @get-title="filterTitle"
                  @get-type="filterType"/>
            </div>
          </b-row>

          <b-row>
            <b-col cols="7">
              <!-- Map -->
              <div class="map">
                <h5> 4. Map </h5>
                <p>Abila is a city of the island of Kronos, it has a lot of restaurants, cafes and activities to do on the freetime.
                <br><span class="subtext"> The colored lines represents the paths of the employees. Each path has a different color.
                    Pass over the lines to see who drove that itinerary.
                  The locations are also shown: Pass over the locations to see their name or click them to filter the barchart.
                    It is possible to hide the locations or reset the map displaying all the paths of the current selected day.</span></p>
                <abila-map :dataGPS="dataGPS"
                           :locations="locations"
                           @reset-map="resetMap"
                           @get-location="getLocation"/>
              </div>
            </b-col>

            <b-col cols="5">
              <!-- Expenses Analysis  -->
              <div class="barcharts">
                <h5> 5. Expenses charts </h5>
                <p> The expenses charts are useful to compare the average expenses of a certain employee or company branch to the others.
                <br><span class="subtext"> Filter the data using the treemap above, the location list and the timeline. To remove the locations filter click the button below.</span></p>
                <b-row>
                  <b-button id="location-btn"
                      size="sm"
                      variant="light"
                      @click="resetLocation()"> All locations
                  </b-button>
                </b-row>
                <b-row>
                  <div class="expensesAnalysis" >
                    <expenses-chart :expenses="expensesCC"
                                    :categories="categoriesCC"
                                    title="Credit Card data"/>
                  </div>
                </b-row>

                <b-row>
                  <div class="expensesAnalysis">
                    <expenses-chart :expenses="expensesLC"
                                   :categories="categoriesLC"
                                    title="Loyalty Card data"/>
                 </div>
                </b-row>
              </div>
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
      selectedType: undefined,
      selectedTitle:null,
      selectedDateRange: null,
      first: true,
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
    resetMap() {
      console.log('map reset');
      this.updateTime([null, null]);
    },
    resetLocation() {
      this.location = null;
      this.getLocation(null);
    },
    getLocation(location) {
      if (location !== 'Driving' && location !== 'Loyalty Card') {
        this.selectedLocation = location;
        console.log(this.selectedLocation, this.selectedEmployee, this.selectedType, this.selectedTitle)
        if(this.selectedEmployee !== null){
            this.filterChartPerEmployee(this.selectedEmployee, this.selectedLocation);
        } else if (this.selectedTitle !== null) {
          this.filterChartPerTitleType(this.selectedTitle, byEmpTitle, this.selectedLocation);
        } else {
          this.filterChartPerTitleType(this.selectedType, byEmpType, this.selectedLocation);
        }

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
      /* given an employee it creates the chart related to the CC expenses and the loyalty card*/
      //this.resetFiltersEmployees();
        byEmpName.filterAll();
        byEmpTitle.filterAll();
        byEmpType.filterAll()

      //byDateCC.filterRange(this.selectedDateRange).top(Infinity);
      //byDateLC.filterRange(this.selectedDateRange).top(Infinity);

      if (location !== null){
        byLocationCC.filterExact(location).top(Infinity);
        byLocationLC.filterExact(location).top(Infinity);
        this.categoriesCC = [['Avg price by', name, 'at', location],
          ['Avg price by','the other' ,'employees at', location]];
        this.categoriesLC = [['Avg loyalty lard', 'value for', name, 'at', location],
          ['Avg loyalty card', 'value for','the other','employees at', location]];
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
      //this.resetFiltersEmployees();
      //byDateCC.filterRange(this.selectedDateRange).top(Infinity);
      //byDateLC.filterRange(this.selectedDateRange).top(Infinity);
      byEmpName.filterAll();
      byEmpTitle.filterAll();
      byEmpType.filterAll()

      if (location !== null){
        byLocationCC.filterExact(location).top(Infinity);
        byLocationLC.filterExact(location).top(Infinity);
      }

      if (secName === undefined) {
        let allCC = this.avgPrice(byNameCC.filterAll().top(Infinity));
        this.expensesCC = [allCC]

        let allLC = this.avgPrice(byNameLC.filterAll().top(Infinity));
        this.expensesLC = [allLC]
        if (location !== null){
          this.categoriesCC = ['Average expenses of all the employees at ' + location]
          this.categoriesLC = ['Average loyalty card value of all the employees at '+ location]
        } else{
          this.categoriesCC = ['Average expenses of all the employees']
          this.categoriesLC = ['Average loyalty card value of all the employees']
        }

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
        this.expensesCC = [sectionCC, othersCC];

        let othersLC = this.avgPrice(byNameLC.filterFunction(function (d) {
          return names.indexOf(d) === -1;
        }).top(Infinity));

        let sectionLC = this.avgPrice(byNameLC.filterFunction(function (d) {
          return names.indexOf(d) > -1;
        }).top(Infinity));
        this.expensesLC = [sectionLC, othersLC];

        if (location != null) {
          this.categoriesCC = [['Average', 'expenses of ', secName, 'employees at', location],
            ['Average', 'expenses of','the other','employees at', location]]
          this.categoriesLC = [['Average', 'loyalty card ', 'value of', secName, 'employees at', location ],
            ['Average','loyalty card ', 'value of ', 'the other','employees at', location]]

        } else {
          this.categoriesCC = [['Average', 'expenses of ', secName, 'employees'],
            ['Average', 'expenses of', 'the other employees']]
          this.categoriesLC = [['Average loyalty', ' card value of', secName, 'employees'],
            ['Average loyalty','card value of ','the other employees']]

        }
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
      this.selectedEmployee = name;
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
      byDateCC.filterRange([min,max]);
      byDateLC.filterRange([min,max]);
      if (this.selectedTitle !== null) {
        this.filterTitle(this.selectedTitle);
      } else if (this.selectedEmployee !== null){
        this.filterEmployee(this.selectedEmployee);
      } else{
        this.filterType(this.selectedType);
      }
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
      this.selectedDateRange = range;
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
      byDateCC.filterRange(range);
      byDateLC.filterRange(range);
      if (this.selectedTitle !== null) {
        this.filterTitle(this.selectedTitle);
      } else if (this.selectedEmployee !== null){
        this.filterEmployee(this.selectedEmployee);
      } else{
        this.filterType(this.selectedType);
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

          this.filterChartPerTitleType(undefined, byEmpType, null);
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
  margin: 10px auto auto auto;
}
.subtext {
  font-style: italic;
  font-size: 80%;
}
p {
  font-size:90%;
  text-align: left;
}
.eventTimeline {
  margin-top:10px;
}
.daySelection{
  padding: 0 15px 2px 5px;
  margin: auto 10px 10px 5px;
}

#tmComponent {
  width: 98%;
  height: 90%;
  margin-top: -20px;
  padding-bottom: 10px;
  padding-top: -15px;
}
.treemap {
  margin: 5px auto 5px auto;
}
.barcharts {
  margin:5px auto auto auto;
}
.map {
  margin-top:5px;
}
#location-btn {
  width: 40%;
  margin: auto auto auto auto;
}

</style>