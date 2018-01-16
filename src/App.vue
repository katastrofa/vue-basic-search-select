<template>
  <div id="app">
    <div class="ui segment">
      <p>Normal style</p>
      <basic-search-select :options="dataOptions" placeholder="Select Movie" tab-index="10" />
      <basic-search-select :options="dataOptions2" placeholder="Some text" tab-index="20" />
    </div>
    <div class="ui inverted segment">
      <p>Inverted</p>
      <basic-search-select :options="dataOptions" placeholder="Select Movie" tab-index="30" />
      <basic-search-select :options="dataOptions2" placeholder="Some text" tab-index="40" />
    </div>
  </div>
</template>

<script>
import BasicSearchSelect from './components/BasicSearchSelect.vue'

let someEntries = [
  {value: 1, label: '01: Vertigo'},
  {value: 2, label: '02: JCVD'},
  {value: 3, label: '03: Batman'},
  {value: 4, label: '04: Star Wars'},
  {value: 5, label: '05: Mission Impossible: Ghost Protocol'},
  {value: 6, label: '06: Enter the Void'}
]

let someEntries2 = [
  {value: 1, label: '01: Vertigo'},
  {value: 2, label: '02: JCVD'},
  {value: 3, label: '03: Batman'},
  {value: 4, label: '04: Star Wars'},
  {value: 5, label: '05: Mission Impossible: Ghost Protocol'},
  {value: 6, label: '06: Enter the Void'},
  {value: 7, label: '07: Scott Pilgrim vs. the World'},
  {value: 8, label: '08: Lord of the Rings'},
  {value: 9, label: '09: Se7en'},
  {value: 10, label: '10: Catch Me If You Can'},
  {value: 11, label: '11: Touch of Evil'},
  {value: 12, label: '12: Napoleon Dynamite'},
  {value: 13, label: '13: Kiss Kiss Bang Bang'},
  {value: 14, label: '14: Fight Club'},
  {value: 15, label: '15: Juno'},
  {value: 16, label: '16: The Shining'},
  {value: 17, label: '17: Snatch'},
  {value: 18, label: '18: 101 Dalmatians'},
  {value: 19, label: '19: The Girl with the Dragon Tattoo'}
]

export default {
  name: 'app',
  components: {
    BasicSearchSelect
  },
  computed: {
    dataOptions: function () {
      return someEntries
    },
    dataOptions2: function () {
      return someEntries2
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 150px;
  max-width: 400px;
}
</style>
