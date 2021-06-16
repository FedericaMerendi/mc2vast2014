<template>
  <div>
    <p> Hello There! </p>
    <b-container>
      <b-row id="firstRow" >
        <b-col id="userData">
          <Employee v-bind:data="data"
                    v-bind:encoding="encoding"></Employee>
        </b-col>
        <b-col class="right_viz">
          <b-row>
            <div class="map">
              <h3> Map </h3>
              <AbilaMap></AbilaMap>
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
const d3 = require('d3');
import crossfilter from 'crossfilter2';
import AbilaMap from "./AbilaMap";
import Employee from "./Employee";

let cars;
let loyalty_cards;
let credit_cards;
let dLocationLC;
let gps;

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
      data: [],
      dLocationLC: [],
      encoding: {
        x: {field:'timestamp', type: 'temporal'},
        y: {field:'lastName', type: 'nominal'},
        size: {field:'price', type:'quantitative'},
        color: {field: "location", type: "nominal"},
      },
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
         /* let cfCars = crossfilter(cars);
          dEmployment = cfCars.dimension((d) =>{ return d.currentEmploymentType});
          console.log(dEmployment.group().all());
          dSubject = cfCars.dimension((d) => {return d.lastName + " " + d.firstName});
          console.log(dSubject.group().all());
*/
          this.cars = cars;
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
          this.credit_cards = credit_cards;
          this.data = credit_cards;
        });

    d3.csv('/data/reduced_gps.csv')
        .then((rows) => {
          gps = rows
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
.filters, .map, #userData {
  background-color: beige;
  margin: 1px;
}
</style>