<template>
<div class="dashboard">
 <!-- <vega-lite :data=data
              mark="tick"
             :encoding="encoding"
  @click="selectedEmployee(data.firstName, data.lastName)"/>
-->
  <b-list-group class="employeeList">
    <b-list-group-item v-for="p in dataEmployees"
                       :key="p.id"
                       @click="selectedEmployee(p.id)">
      {{p.id}}, {{p.lastName}},{{p.firstName}},{{p.carID}}, {{p.currentEmploymentTitle}}, {{p.currentEmploymentType}}
    </b-list-group-item>
  </b-list-group>
</div>
</template>

<script>
//import VueVega from 'vue-vega'

export default {
  name: "Employee",
  //props: ['data', 'encoding'],
  props: {
    dataEmployees: Array,
  },
  methods: {
    selectedEmployee(id){
      console.log(id);
      this.$emit('get-employee-name', id);
    }
  }
}


</script>

<style scoped>

</style>