<template>
<div>
  <b-list-group class="employeeList">
    <b-list-group-item v-for="p in cars" :key="p.id">
      {{p.lastName}},{{p.firstName}},{{p.carID}}, {{p.currentEmploymentTitle}}, {{p.currentEmploymentType}}
    </b-list-group-item>
  </b-list-group>
</div>
</template>

<script>
export default {
  name: "Employee",
  props: ['cars'],
}
</script>

<style scoped>

</style>