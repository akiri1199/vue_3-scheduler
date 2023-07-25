<template>
  <div id="app">
    <sc :setting="setting" :schedule-data="scData" @finish-Event="finishEvent"></sc>
  </div>
  <div>
    <ul>
      <li v-for="(values, key) in state.items" :key="key">
        {{ key }}: {{ values.join(', ') }}
      </li>
    </ul>
  </div>
</template>
<script>
import schedulerLite from "./components/schedulerLite";
import { reactive } from 'vue';
const setting = {
  unit: 24, //  it is division of hour set a among 1,2,3,4,5,6,8,9,10,12,15,16,18,20,24,30,32,36,40,45,48,60
  locale: 'es-ES' // en-GB  or es-ES
};
const sampleData = {   // insert saved Json data
  Monday: ['01:30'],
  ThuesDay: [],
  Wednesday: ['00:00', '03:30'],
  Thursday: ['02:30'],
  Friday: [],
  Saturday: [],
  Sunday: []
};

export default {
  name: "App",
  components: {
    sc: schedulerLite,
  },
  data: function () {
    return {
      setting: setting,
      scData: sampleData,
    };
  },
  methods: {
    finishEvent(result_arr) {
      this.state.items = result_arr;
    }
  },
  setup() {
    const state = reactive({
      items: []
    });

    return { state };
  }
};
</script>


