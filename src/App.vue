<template>
  <div id="app">
    <sc :setting="setting" :schedule-data="scData" @finish-Event="finishEvent"></sc>
  </div>
</template>
<script>
import schedulerLite from "./components/schedulerLite";

const divisionSetting = {
  unit: 15, //  it is division of hour set a among 1,2,3,4,5,6,8,9,10,12,15,16,18,20,24,30,32,36,40,45,48,60
};
const sampleData = {   // insert saved Json data
  Monday: ['01:40'],
  ThuesDay: [],
  Wednesday: ['00:20', '03:40'],
  Thursday: ['02:40'],
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
      setting: divisionSetting,
      scData: sampleData,
    };
  },
  methods: {
    finishEvent(result_arr) {
      console.log(result_arr);
    }
  },
};
</script>


