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
          "<b> Employee: </b>Mies Haber Ruscella<br> <b> Employment Title: </b>Administrative Assistant<br><b> Employment Type: </b>Engineering<br><b> Car: </b> No car assigned",
          "<b> Employee: </b>Onda Marin<br> <b> Employment Title: </b>Drill Site Manager<br><b> Employment Type: </b>Engineering<br><b> Car: </b> 26",
          "<b> Employee: </b>Borrasca Isande<br> <b> Employment Title: </b>Drill Technician<br><b> Employment Type: </b>Engineering<br><b> Car: </b> 7",
          "<b> Employee: </b>Calzas Axel<br> <b> Employment Title: </b>Drill Technician<br><b> Employment Type: </b>Engineering<br><b> Car: </b> 9",
          "<b> Employee: </b>Orilla Elsa<br> <b> Employment Title: </b>Drill Technician<br><b> Employment Type: </b>Engineering<br><b> Car: </b> 28 (Broken GPS)",
          "<b> Employee: </b>Orilla Kare<br> <b> Employment Title: </b>Drill Technician<br><b> Employment Type: </b>Engineering<br><b> Car: </b> 27",
          "<b> Employee: </b>Tempestad Brand<br> <b> Employment Title: </b>Drill Technician<br><b> Employment Type: </b>Engineering<br><b> Car: </b> 33",
          "<b> Employee: </b>Azada Lars<br> <b> Employment Title: </b>Engineer<br><b> Employment Type: </b>Engineering<br><b> Car: </b> 2",
          "<b> Employee: </b>Balas Felix<br> <b> Employment Title: </b>Engineer<br><b> Employment Type: </b>Engineering<br><b> Car: </b> 3",
          "<b> Employee: </b>Dedos Lidelse<br> <b> Employment Title: </b>Engineering Group Manager<br><b> Employment Type: </b>Engineering<br><b> Car: </b> 14",
          "<b> Employee: </b>Frente Birgitta<br> <b> Employment Title: </b>Geologist<br><b> Employment Type: </b>Engineering<br><b> Car: </b> 18",
          "<b> Employee: </b>Nubarron Adra<br> <b> Employment Title: </b>Geologist<br><b> Employment Type: </b>Engineering<br><b> Car: </b> 25",
          "<b> Employee: </b>Cazar Gustav<br> <b> Employment Title: </b>Hydraulic Technician<br><b> Employment Type: </b>Engineering<br><b> Car: </b> 11",
          "<b> Employee: </b>Frente Vira<br> <b> Employment Title: </b>Hydraulic Technician<br><b> Employment Type: </b>Engineering<br><b> Car: </b> 19",
          "<b> Employee: </b>Bramar Mat<br> <b> Employment Title: </b>Administrative Assistant<br><b> Employment Type: </b>Executive<br><b> Car: </b> No car assigned",
          "<b> Employee: </b>Lagos Linda<br> <b> Employment Title: </b>Administrative Assistant<br><b> Employment Type: </b>Executive<br><b> Car: </b> No car assigned",
          "<b> Employee: </b>Pantanal Rachel<br> <b> Employment Title: </b>Administrative Assistant<br><b> Employment Type: </b>Executive<br><b> Car: </b> No car assigned",
          "<b> Employee: </b>Ribera Anda<br> <b> Employment Title: </b>Administrative Assistant<br><b> Employment Type: </b>Executive<br><b> Car: </b> No car assigned",
          "<b> Employee: </b>Vasco-Pais Willem<br> <b> Employment Title: </b>Environmental Safety Advisor<br><b> Employment Type: </b>Executive<br><b> Car: </b> 35",
          "<b> Employee: </b>Sanjorge Jr. Sten<br> <b> Employment Title: </b>President/CEO<br><b> Employment Type: </b>Executive<br><b> Car: </b> 31",
          "<b> Employee: </b>Barranco Ingrid<br> <b> Employment Title: </b>SVP/CFO<br><b> Employment Type: </b>Executive<br><b> Car: </b> 4",
          "<b> Employee: </b>Campo-Corrente Ada<br> <b> Employment Title: </b>SVP/CIO<br><b> Employment Type: </b>Executive<br><b> Car: </b> 10",
          "<b> Employee: </b>Strum Orhan<br> <b> Employment Title: </b>SVP/COO<br><b> Employment Type: </b>Executive<br><b> Car: </b> 32",
          "<b> Employee: </b>Ovan Bertrand<br> <b> Employment Title: </b>Facilities Group Manager<br><b> Employment Type: </b>Facilities<br><b> Car: </b> 29",
          "<b> Employee: </b>Arpa Emile<br> <b> Employment Title: </b>Janitor<br><b> Employment Type: </b>Facilities<br><b> Car: </b> No car assigned",
          "<b> Employee: </b>Awelon Varro<br> <b> Employment Title: </b>Janitor<br><b> Employment Type: </b>Facilities<br><b> Car: </b> No car assigned",
          "<b> Employee: </b>Coginian Dante<br> <b> Employment Title: </b>Lead Janitor<br><b> Employment Type: </b>Facilities<br><b> Car: </b> No car assigned",
          "<b> Employee: </b>Hafon Albina<br> <b> Employment Title: </b>Truck Driver<br><b> Employment Type: </b>Facilities<br><b> Car: </b> No car assigned",
          "<b> Employee: </b>Hawelon Benito<br> <b> Employment Title: </b>Truck Driver<br><b> Employment Type: </b>Facilities<br><b> Car: </b> No car assigned",
          "<b> Employee: </b>Hawelon Claudio<br> <b> Employment Title: </b>Truck Driver<br><b> Employment Type: </b>Facilities<br><b> Car: </b> No car assigned",
          "<b> Employee: </b>Mies Henk<br> <b> Employment Title: </b>Truck Driver<br><b> Employment Type: </b>Facilities<br><b> Car: </b> No car assigned",
          "<b> Employee: </b>Morlun Adan<br> <b> Employment Title: </b>Truck Driver<br><b> Employment Type: </b>Facilities<br><b> Car: </b> No car assigned",
          "<b> Employee: </b>Morlun Valeria<br> <b> Employment Title: </b>Truck Driver<br><b> Employment Type: </b>Facilities<br><b> Car: </b> No car assigned",
          "<b> Employee: </b>Morluniau Cecilia<br> <b> Employment Title: </b>Truck Driver<br><b> Employment Type: </b>Facilities<br><b> Car: </b> No car assigned",
          "<b> Employee: </b>Nant Irene<br> <b> Employment Title: </b>Truck Driver<br><b> Employment Type: </b>Facilities<br><b> Car: </b> No car assigned",
          "<b> Employee: </b>Scozzese Dylan<br> <b> Employment Title: </b>Truck Driver<br><b> Employment Type: </b>Facilities<br><b> Car: </b> No car assigned",
          "<b> Employee: </b>Forluniau Carla<br> <b> Employment Title: </b>Administrative Assistant<br><b> Employment Type: </b>Information Technology<br><b> Car: </b> No car assigned",
          "<b> Employee: </b>Bergen Linnea<br> <b> Employment Title: </b>IT Group Manager<br><b> Employment Type: </b>Information Technology<br><b> Car: </b> 6",
          "<b> Employee: </b>Alcazar Lucas<br> <b> Employment Title: </b>IT Helpdesk<br><b> Employment Type: </b>Information Technology<br><b> Car: </b> 1",
          "<b> Employee: </b>Baza Isak<br> <b> Employment Title: </b>IT Technician<br><b> Employment Type: </b>Information Technology<br><b> Car: </b> 5",
          "<b> Employee: </b>Calixto Nils<br> <b> Employment Title: </b>IT Technician<br><b> Employment Type: </b>Information Technology<br><b> Car: </b> 8",
          "<b> Employee: </b>Flecha Sven<br> <b> Employment Title: </b>IT Technician<br><b> Employment Type: </b>Information Technology<br><b> Car: </b> 17",
          "<b> Employee: </b>Lais Cornelia<br> <b> Employment Title: </b>Administrative Assistant<br><b> Employment Type: </b>Security<br><b> Car: </b> No car assigned",
          "<b> Employee: </b>Herrero Kanon<br> <b> Employment Title: </b>Badging Office<br><b> Employment Type: </b>Security<br><b> Car: </b> 22",
          "<b> Employee: </b>Lagos Varja<br> <b> Employment Title: </b>Badging Office<br><b> Employment Type: </b>Security<br><b> Car: </b> 23",
          "<b> Employee: </b>Fusil Stenig<br> <b> Employment Title: </b>Building Control<br><b> Employment Type: </b>Security<br><b> Car: </b> 20",
          "<b> Employee: </b>Mies Minke<br> <b> Employment Title: </b>Perimeter Control<br><b> Employment Type: </b>Security<br><b> Car: </b> 24",
          "<b> Employee: </b>Osvaldo Hennie<br> <b> Employment Title: </b>Perimeter Control<br><b> Employment Type: </b>Security<br><b> Car: </b> 21",
          "<b> Employee: </b>Vann Edvard<br> <b> Employment Title: </b>Perimeter Control<br><b> Employment Type: </b>Security<br><b> Car: </b> 34",
          "<b> Employee: </b>Vann Isia<br> <b> Employment Title: </b>Perimeter Control<br><b> Employment Type: </b>Security<br><b> Car: </b> 16",
          "<b> Employee: </b>Resumir Felix<br> <b> Employment Title: </b>Security Group Manager<br><b> Employment Type: </b>Security<br><b> Car: </b> 30",
          "<b> Employee: </b>Bodrogi Loreto<br> <b> Employment Title: </b>Site Control<br><b> Employment Type: </b>Security<br><b> Car: </b> 15",
          "<b> Employee: </b>Cocinaro Hideki<br> <b> Employment Title: </b>Site Control<br><b> Employment Type: </b>Security<br><b> Car: </b> 12",
          "<b> Employee: </b>Ferro Inga<br> <b> Employment Title: </b>Site Control<br><b> Employment Type: </b>Security<br><b> Car: </b> 13",
        ],
      }],
    }
  },
  methods: {
    selection(d) {
      let typeList = ['Engineering','Executive','Facilities','Information Technology','Security', 'All'];
      let titleList  = ['Administrative Assistant (Engineering)', 'Drill Site Manager','Drill Technician',
          'Engineer', 'Engineering Group Manager', 'Geologist', 'Hydraulic Technician',
          'Administrative Assistant (Executive)', 'Environmental Safety Advisor',
          'President/CEO', 'SVP/CFO', 'SVP/CIO', 'SVP/COO',
          'Facilities Group Manager', 'Janitor', 'Lead Janitor', 'Truck Driver',
          'Administrative Assistant (Information Technology)', 'IT Group Manager', 'IT Helpdesk',
          'IT Technician', 'Administrative Assistant (Security)', 'Badging Office',
          'Building Control', 'Perimeter Control','Security Group Manager',
          'Site Control'];

      if (d.points[0].label in typeList){
        this.$emit('get-type', d.points[0].label);
      } else if (d.points[0].label in titleList) {
        this.$emit('get-title', d.points[0].label);
      } else {
        this.$emit('get-employee', d.points[0].label);
      }

    }
  },
}
</script>

<style scoped>
</style>
