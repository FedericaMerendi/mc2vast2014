<template>
<div>
  <p> Hello There!</p>
  <b-container>
    <b-row id="firstRow" >
      <b-col id="userData">
        User data is going here
      </b-col>
      <b-col id="map">
        The map is going here
      </b-col>
      <b-list-group class="personList">
        <b-list-group-item v-for="p in persons" :key="p.id">
          {{p.lastName}},{{p.firstName}},{{p.carID}}, {{p.currentEmploymentTitle}}, {{p.currentEmploymentType}}
        </b-list-group-item>
      </b-list-group>

    </b-row>
  </b-container>
</div>
</template>

<script>
const d3 = require('d3');

export default {
  name: "MainLayout",
  data(){
    return {
      persons: [],
      loyalty_card: [],
      credit_card: [],
      gps: [],
    }
  },
  mounted() {
    d3.csv('/data/car-assignments-ids.csv')
        .then((rows) => {
          const persons = rows
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
          //console.log(persons);
          this.persons = persons;
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
          this.loyalty_card = lc;
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
          this.credit_card = cc;
        });

    /* d3.csv('/data/gps.csv')
        .then((rows) => {
          const gps = rows
              .map((row) => {
                return {
                  timestamp: new Date(row.timestamp),
                  carID: +row.id,
                  lat: +row.lat,
                  long: +row.long,
                };
              });
          //console.log(gps);
          this.gps = gps;
        });
     */
  },
}

</script>

<style scoped>
#map, #userData {
  background-color: bisque;
  margin: 1px;
}
</style>