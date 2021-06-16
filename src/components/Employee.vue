<template>
<div class="dashboard">
  <vega-lite :data=data
              mark="tick" :encoding="encoding"/>

  <!--<b-list-group class="employeeList">
    <b-list-group-item v-for="p in cars" :key="p.id">
      {{p.lastName}},{{p.firstName}},{{p.carID}}, {{p.currentEmploymentTitle}}, {{p.currentEmploymentType}}
    </b-list-group-item>
  </b-list-group> -->
</div>
</template>

<script>
//import VueVega from 'vue-vega'

export default {
  name: "Employee",
  props: ['data', 'encoding'],
}


</script>

<style scoped>

</style>