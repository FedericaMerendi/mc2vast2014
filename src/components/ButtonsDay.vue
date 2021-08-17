<template>
  <div>
    <b-button-group size="sm"
                    v-for="d in days_buttons"
                    :key="d.day" >
      <b-button variant="light"
                @click="selectDay(d.day)"> {{d.weekday}} {{d.day}} </b-button>
    </b-button-group>
  </div>

</template>

<script>
export default {
  name: "ButtonsDay",
  data() {
    return{
      days_buttons: [
        {weekday:'Mon', day: '6'},
        {weekday:'Tue', day: '7'},
        {weekday:'Wed', day: '8'},
        {weekday:'Thu', day: '9'},
        {weekday:'Fri', day: '10'},
        {weekday:'Sat', day: '11'},
        {weekday:'Sun', day: '12'},
        {weekday:'Mon', day: '13'},
        {weekday:'Tue', day: '14'},
        {weekday:'Wed', day: '15'},
        {weekday:'Thu', day: '16'},
        {weekday:'Fri', day: '17'},
        {weekday:'Sat', day: '18'},
        {weekday:'Sun', day: '19'},
      ],
    }
  },
  methods: {
    selectDay(day) {
      this.$emit('get-day', day);
    },
  }
}
</script>

<style scoped>
</style>