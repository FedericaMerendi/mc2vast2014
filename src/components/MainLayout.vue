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
    </b-row>
  </b-container>
</div>
</template>

<script>
export default {
  name: "MainLayout",
}


</script>

<style scoped>
#map, #userData {
  background-color: bisque;
  margin: 1px;
}
</style>