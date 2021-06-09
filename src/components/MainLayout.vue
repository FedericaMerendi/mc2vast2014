<template>
  <div>
    <p> Hello There!</p>
    <b-container>
      <b-row id="firstRow" >
        <b-col id="userData">
          <Employee v-bind:cars="cars"></Employee>
        </b-col>
        <b-col class="map">
          <h3> Map</h3>
          <div style="height:500px">
            <AbilaMap> Hello</AbilaMap>
          </div>
        </b-col>

      </b-row>
    </b-container>
  </div>
</template>

<script>
const d3 = require('d3');
import crossfilter from 'crossfilter2';
import AbilaMap from "./AbilaMap";
import Employee from "./Employee";

let cfCars;
let cars;

export default {
  name: "MainLayout",
  components: {
    AbilaMap,
    Employee,
  },
  data(){
    return {
      cars: [],
      loyalty_cards: [],
      credit_cards: [],
      gps: [],
    }
  },
  mounted() {
    d3.csv('/data/car-assignments-ids.csv')
        .then((rows) => {
          cars = rows
              .map((row) => {
                return {
                  id: row.PersonID,
                  lastName: row.LastName,
                  firstName: row.FirstName,
                  carID: +row.CarID,
                  currentEmploymentType: row.CurrentEmploymentType,
                  currentEmploymentTitle: row.CurrentEmploymentTitle
                };
              });
          cfCars = crossfilter(cars);
          console.log(cfCars.groupAll().reduceCount().value());
          this.cars = cars;
        });

    d3.csv('/data/loyalty_data.csv')
        .then((rows) => {
          const lc = rows
              .map((row) => {
                return {
                  day: new Date(row.timestamp),
                  location: row.location,
                  price: +row.price,
                  firstName: row.FirstName,
                  lastName: row.LastName,
                };
              });
          //console.log(lc);
          this.loyalty_cards = lc;
        });

    d3.csv('/data/cc_data.csv')
        .then((rows) => {
          const cc = rows
              .map((row) => {
                return {
                  timestamp: new Date(row.timestamp),
                  location: row.location,
                  price: +row.price,
                  firstName: row.FirstName,
                  lastName: row.LastName,
                };
              });
          //console.log(cc);
          this.credit_cards = cc;
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
          //console.log(gps);
          this.gps = gps;
        });

  },
}

</script>

<style scoped>
#map, #userData {
  background-color: bisque;
  margin: 1px;
}
</style>