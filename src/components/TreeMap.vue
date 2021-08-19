<template>
  <div>
    <plotly :data="data"
            :layout="layout"
            ref="treemap"
            autoResize="true"
            :display-mode-bar="false"
    />
  </div>
</template>

<script>
import {Plotly} from 'vue-plotly'
export default {
  name:'TreeMap',
  components: {
    Plotly,
  },
  data() {
    return {
      data: [{
        type: "treemap",
        labels: ['Engineering','Executive','Facilities','Information Technology','Security',
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
        parents: ['','','','','','Engineering','Engineering','Engineering','Engineering','Engineering','Engineering','Engineering',
          'Executive','Executive','Executive','Executive','Executive','Executive',
          'Facilities', 'Facilities', 'Facilities', 'Facilities',
          'Information Technology','Information Technology','Information Technology','Information Technology',
          'Security', 'Security', 'Security', 'Security', 'Security', 'Security',
          'Administrative Assistant (Engineering)', 'Drill Site Manager',
          'Drill Technician', 'Drill Technician', 'Drill Technician',
          'Drill Technician', 'Drill Technician', 'Engineer', 'Engineer',
          'Engineering Group Manager', 'Geologist', 'Geologist',
          'Hydraulic Technician', 'Hydraulic Technician',
          'Administrative Assistant (Executive)', 'Administrative Assistant (Executive)',
          'Administrative Assistant (Executive)', 'Administrative Assistant (Executive)',
          'Environmental Safety Advisor', 'President/CEO', 'SVP/CFO', 'SVP/CIO',
          'SVP/COO', 'Facilities Group Manager', 'Janitor', 'Janitor', 'Lead Janitor',
          'Truck Driver', 'Truck Driver', 'Truck Driver', 'Truck Driver', 'Truck Driver',
          'Truck Driver', 'Truck Driver', 'Truck Driver', 'Truck Driver',
          'Administrative Assistant (Information Technology)',
          'IT Group Manager', 'IT Helpdesk', 'IT Technician', 'IT Technician',
          'IT Technician', 'Administrative Assistant (Security)', 'Badging Office',
          'Badging Office', 'Building Control', 'Perimeter Control',
          'Perimeter Control', 'Perimeter Control', 'Perimeter Control',
          'Security Group Manager', 'Site Control', 'Site Control', 'Site Control'
        ],
      }],
      layout: {
        title: "GAStech employees"
      }
    }
  },
  mounted() {
    this.$refs.treemap.$on('click', d => {
      console.log(d.points[0].data.level,d.points[0].data);
    });

  }
}
</script>

<style>

</style>
