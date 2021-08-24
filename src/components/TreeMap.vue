<template>
  <div class="treemap">
    <plotly
        :data="data"
        :layout="layout"
        autoResize="true"
        :display-mode-bar="false"
        @click="selection"

    />
  </div>
</template>

<script>
import {Plotly} from 'vue-plotly';

export default {
  name:'TreeMap',
  components: {
    Plotly,
  },
  data() {
    return {
      layout: {
        margin: {
          l: 10,
          r: 10,
          b: 10,
          t: 30,
          pad: 4
        },
      },
      data: [{
        type: "treemap",
        labels:['Engineering','Executive','Facilities','Information Technology','Security',
          'Administrative Assistant (Engineering)', 'Drill Site Manager','Drill Technician',
          'Engineer', 'Engineering Group Manager', 'Geologist', 'Hydraulic Technician',
          'Administrative Assistant (Executive)', 'Environmental Safety Advisor',
          'President/CEO', 'SVP/CFO', 'SVP/CIO', 'SVP/COO',
          'Facilities Group Manager', 'Janitor', 'Lead Janitor', 'Truck Driver',
          'Administrative Assistant (Information Technology)', 'IT Group Manager', 'IT Helpdesk',
          'IT Technician', 'Administrative Assistant (Security)', 'Badging Office',
          'Building Control', 'Perimeter Control','Security Group Manager',
          'Site Control', 'Mies Haber Ruscella', 'Onda Marin','Borrasca Isande',
          'Calzas Axel', 'Orilla Elsa', 'Orilla Kare', 'Tempestad Brand',
          'Azada Lars', 'Balas Felix', 'Dedos Lidelse', 'Frente Birgitta',
          'Nubarron Adra', 'Cazar Gustav', 'Frente Vira', 'Bramar Mat',
          'Lagos Linda', 'Pantanal Rachel', 'Ribera Anda', 'Vasco-Pais Willem',
          'Sanjorge Jr. Sten','Barranco Ingrid','Campo-Corrente Ada', 'Strum Orhan',
          'Ovan Bertrand', 'Arpa Emile', 'Awelon Varro', 'Coginian Dante',
          'Hafon Albina', 'Hawelon Benito', 'Hawelon Claudio', 'Mies Henk',
          'Morlun Adan', 'Morlun Valeria', 'Morluniau Cecilia', 'Nant Irene',
          'Scozzese Dylan', 'Forluniau Carla', 'Bergen Linnea', 'Alcazar Lucas',
          'Baza Isak', 'Calixto Nils', 'Flecha Sven', 'Lais Cornelia',
          'Herrero Kanon', 'Lagos Varja', 'Fusil Stenig', 'Mies Minke',
          'Osvaldo Hennie', 'Vann Edvard', 'Vann Isia', 'Resumir Felix',
          'Bodrogi Loreto', 'Cocinaro Hideki', 'Ferro Inga'
        ],
        parents: ['All','All','All','All','All',
          'Engineering','Engineering','Engineering','Engineering','Engineering','Engineering','Engineering',
          'Executive','Executive','Executive','Executive','Executive','Executive',
          'Facilities', 'Facilities', 'Facilities', 'Facilities',
          'Information Technology','Information Technology','Information Technology','Information Technology',
          'Security', 'Security', 'Security', 'Security', 'Security', 'Security',
          'Administrative Assistant (Engineering)',
          'Drill Site Manager',
          'Drill Technician', 'Drill Technician', 'Drill Technician',
          'Drill Technician', 'Drill Technician',
          'Engineer', 'Engineer',
          'Engineering Group Manager',
          'Geologist', 'Geologist',
          'Hydraulic Technician', 'Hydraulic Technician',
          'Administrative Assistant (Executive)', 'Administrative Assistant (Executive)',
          'Administrative Assistant (Executive)', 'Administrative Assistant (Executive)',
          'Environmental Safety Advisor',
          'President/CEO', 'SVP/CFO', 'SVP/CIO', 'SVP/COO',
          'Facilities Group Manager',
          'Janitor', 'Janitor', 'Lead Janitor',
          'Truck Driver', 'Truck Driver', 'Truck Driver', 'Truck Driver', 'Truck Driver',
          'Truck Driver', 'Truck Driver', 'Truck Driver', 'Truck Driver',
          'Administrative Assistant (Information Technology)',
          'IT Group Manager', 'IT Helpdesk',
          'IT Technician', 'IT Technician', 'IT Technician',
          'Administrative Assistant (Security)',
          'Badging Office', 'Badging Office',
          'Building Control',
          'Perimeter Control', 'Perimeter Control', 'Perimeter Control', 'Perimeter Control',
          'Security Group Manager',
          'Site Control', 'Site Control', 'Site Control'
        ],
        text:["", "", "", "", "",
          "", "", "", "", "", "", "",
          "", "", "", "", "", "", "",
          "", "", "", "",
          "", "", "", "",
          "", "", "", "", "",
          "<b> Employment Title: </b>Administrative Assistant<br><b> Employment Type: </b>Engineering<br><b> Car: </b> No car assigned",
          "<b> Employment Title: </b>Drill Site Manager<br><b> Employment Type: </b>Engineering<br><b> Car: </b> 26",
          "<b> Employment Title: </b>Drill Technician<br><b> Employment Type: </b>Engineering<br><b> Car: </b> 7",
          "<b> Employment Title: </b>Drill Technician<br><b> Employment Type: </b>Engineering<br><b> Car: </b> 9",
          "<b> Employment Title: </b>Drill Technician<br><b> Employment Type: </b>Engineering<br><b> Car: </b> 28 (Broken GPS)",
          "<b> Employment Title: </b>Drill Technician<br><b> Employment Type: </b>Engineering<br><b> Car: </b> 27",
          "<b> Employment Title: </b>Drill Technician<br><b> Employment Type: </b>Engineering<br><b> Car: </b> 33",
          "<b> Employment Title: </b>Engineer<br><b> Employment Type: </b>Engineering<br><b> Car: </b> 2",
          "<b> Employment Title: </b>Engineer<br><b> Employment Type: </b>Engineering<br><b> Car: </b> 3",
          "<b> Employment Title: </b>Engineering Group Manager<br><b> Employment Type: </b>Engineering<br><b> Car: </b> 14",
          "<b> Employment Title: </b>Geologist<br><b> Employment Type: </b>Engineering<br><b> Car: </b> 18",
          "<b> Employment Title: </b>Geologist<br><b> Employment Type: </b>Engineering<br><b> Car: </b> 25",
          "<b> Employment Title: </b>Hydraulic Technician<br><b> Employment Type: </b>Engineering<br><b> Car: </b> 11",
          "<b> Employment Title: </b>Hydraulic Technician<br><b> Employment Type: </b>Engineering<br><b> Car: </b> 19",
          "<b> Employment Title: </b>Administrative Assistant<br><b> Employment Type: </b>Executive<br><b> Car: </b> No car assigned",
          "<b> Employment Title: </b>Administrative Assistant<br><b> Employment Type: </b>Executive<br><b> Car: </b> No car assigned",
          "<b> Employment Title: </b>Administrative Assistant<br><b> Employment Type: </b>Executive<br><b> Car: </b> No car assigned",
          "<b> Employment Title: </b>Administrative Assistant<br><b> Employment Type: </b>Executive<br><b> Car: </b> No car assigned",
          "<b> Employment Title: </b>Environmental Safety Advisor<br><b> Employment Type: </b>Executive<br><b> Car: </b> 35",
          "<b> Employment Title: </b>President/CEO<br><b> Employment Type: </b>Executive<br><b> Car: </b> 31",
          "<b> Employment Title: </b>SVP/CFO<br><b> Employment Type: </b>Executive<br><b> Car: </b> 4",
          "<b> Employment Title: </b>SVP/CIO<br><b> Employment Type: </b>Executive<br><b> Car: </b> 10",
          "<b> Employment Title: </b>SVP/COO<br><b> Employment Type: </b>Executive<br><b> Car: </b> 32",
          "<b> Employment Title: </b>Facilities Group Manager<br><b> Employment Type: </b>Facilities<br><b> Car: </b> 29",
          "<b> Employment Title: </b>Janitor<br><b> Employment Type: </b>Facilities<br><b> Car: </b> No car assigned",
          "<b> Employment Title: </b>Janitor<br><b> Employment Type: </b>Facilities<br><b> Car: </b> No car assigned",
          "<b> Employment Title: </b>Lead Janitor<br><b> Employment Type: </b>Facilities<br><b> Car: </b> No car assigned",
          "<b> Employment Title: </b>Truck Driver<br><b> Employment Type: </b>Facilities<br><b> Car: </b> No car assigned",
          "<b> Employment Title: </b>Truck Driver<br><b> Employment Type: </b>Facilities<br><b> Car: </b> No car assigned",
          "<b> Employment Title: </b>Truck Driver<br><b> Employment Type: </b>Facilities<br><b> Car: </b> No car assigned",
          "<b> Employment Title: </b>Truck Driver<br><b> Employment Type: </b>Facilities<br><b> Car: </b> No car assigned",
          "<b> Employment Title: </b>Truck Driver<br><b> Employment Type: </b>Facilities<br><b> Car: </b> No car assigned",
          "<b> Employment Title: </b>Truck Driver<br><b> Employment Type: </b>Facilities<br><b> Car: </b> No car assigned",
          "<b> Employment Title: </b>Truck Driver<br><b> Employment Type: </b>Facilities<br><b> Car: </b> No car assigned",
          "<b> Employment Title: </b>Truck Driver<br><b> Employment Type: </b>Facilities<br><b> Car: </b> No car assigned",
          "<b> Employment Title: </b>Truck Driver<br><b> Employment Type: </b>Facilities<br><b> Car: </b> No car assigned",
          "<b> Employment Title: </b>Administrative Assistant<br><b> Employment Type: </b>Information Technology<br><b> Car: </b> No car assigned",
          "<b> Employment Title: </b>IT Group Manager<br><b> Employment Type: </b>Information Technology<br><b> Car: </b> 6",
          "<b> Employment Title: </b>IT Helpdesk<br><b> Employment Type: </b>Information Technology<br><b> Car: </b> 1",
          "<b> Employment Title: </b>IT Technician<br><b> Employment Type: </b>Information Technology<br><b> Car: </b> 5",
          "<b> Employment Title: </b>IT Technician<br><b> Employment Type: </b>Information Technology<br><b> Car: </b> 8",
          "<b> Employment Title: </b>IT Technician<br><b> Employment Type: </b>Information Technology<br><b> Car: </b> 17",
          "<b> Employment Title: </b>Administrative Assistant<br><b> Employment Type: </b>Security<br><b> Car: </b> No car assigned",
          "<b> Employment Title: </b>Badging Office<br><b> Employment Type: </b>Security<br><b> Car: </b> 22",
          "<b> Employment Title: </b>Badging Office<br><b> Employment Type: </b>Security<br><b> Car: </b> 23",
          "<b> Employment Title: </b>Building Control<br><b> Employment Type: </b>Security<br><b> Car: </b> 20",
          "<b> Employment Title: </b>Perimeter Control<br><b> Employment Type: </b>Security<br><b> Car: </b> 24",
          "<b> Employment Title: </b>Perimeter Control<br><b> Employment Type: </b>Security<br><b> Car: </b> 21",
          "<b> Employment Title: </b>Perimeter Control<br><b> Employment Type: </b>Security<br><b> Car: </b> 34",
          "<b> Employment Title: </b>Perimeter Control<br><b> Employment Type: </b>Security<br><b> Car: </b> 16",
          "<b> Employment Title: </b>Security Group Manager<br><b> Employment Type: </b>Security<br><b> Car: </b> 30",
          "<b> Employment Title: </b>Site Control<br><b> Employment Type: </b>Security<br><b> Car: </b> 15",
          "<b> Employment Title: </b>Site Control<br><b> Employment Type: </b>Security<br><b> Car: </b> 12",
          "<b> Employment Title: </b>Site Control<br><b> Employment Type: </b>Security<br><b> Car: </b> 13",
        ],
        textposition: 'middle center',
      }],
    }
  },
  methods: {
    selection(d) {
      let typeList = ['Engineering','Executive','Facilities','Information Technology','Security', undefined];
      let titleList  = ['Administrative Assistant (Engineering)', 'Drill Site Manager','Drill Technician',
          'Engineer', 'Engineering Group Manager', 'Geologist', 'Hydraulic Technician',
          'Administrative Assistant (Executive)', 'Environmental Safety Advisor',
          'President/CEO', 'SVP/CFO', 'SVP/CIO', 'SVP/COO',
          'Facilities Group Manager', 'Janitor', 'Lead Janitor', 'Truck Driver',
          'Administrative Assistant (Information Technology)', 'IT Group Manager', 'IT Helpdesk',
          'IT Technician', 'Administrative Assistant (Security)', 'Badging Office',
          'Building Control', 'Perimeter Control','Security Group Manager',
          'Site Control'];
    if (typeList.includes(d.points[0].label)){
        this.$emit('get-type', d.points[0].label);
      } else if (titleList.includes(d.points[0].label)) {
        this.$emit('get-title', d.points[0].label);
      } else {
      console.log(d.points[0].label)
        this.$emit('get-employee', d.points[0].label);
      }
    }
  },
}
</script>

<style scoped>
</style>
