<template>
  <div id="app">
    <div style="max-width: 400px">
      <input type="text" name="shit" tabindex="5">
      <basic-select :options="dataOptions" placeholder="Please select" tab-index="10"></basic-select>
      <basic-select :options="dataOptions2" placeholder="Davaj" tab-index="20"></basic-select>
    </div>
  </div>
</template>

<script>
import Hello from './components/Hello'
import BasicSelect from './components/BasicSelect.vue'

let veci = [
  {value: 1, label: '1: Moj Vlastny'},
  {value: 2, label: '2: UK moj'},
  {value: 3, label: '3: Plump fiction'},
  {value: 4, label: '4: Bridesmaids'},
  {value: 5, label: '5: Some shit'},
  {value: 6, label: '6: Nieco uplne ine'}
]

let veci2 = [
  {value: 10, label: '10: Nove veci'},
  {value: 11, label: '11: Dalsia'},
  {value: 12, label: '12: Stupid songs'},
  {value: 13, label: '13: Shitting in the middle of the street'},
  {value: 14, label: '14: And now for something completely different'},
  {value: 15, label: '15: Life of Brian'},
  {value: 16, label: '16: Search for holy grail'},
  {value: 17, label: '17: Erasorhead'},
  {value: 18, label: '18: The ultimate sin'},
  {value: 19, label: '19: This is very long list'},
  {value: 20, label: '20: Maybe even longer'},
  {value: 21, label: '21: Then you would thing'},
  {value: 22, label: '22: I meant think'},
  {value: 23, label: '23: Oh yeah'},
  {value: 24, label: '24: Typical misspellings'}
]

export default {
  name: 'app',
  components: {
    BasicSelect,
    Hello
  },
  computed: {
    dataOptions: function () {
      return veci
    },
    dataOptions2: function () {
      return veci2
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
  margin-top: 60px;
}
</style>
