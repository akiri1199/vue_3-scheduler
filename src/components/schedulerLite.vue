<template>
  <div class="schedule">
    <div>
      <div class="sc-rows" :style="{ width: '7%', height: state.contentH + 'px' }">
        <div :style="{ width: '100%', height: '70px', border: '1px #000 solid', borderRadius: '5px' }"> </div>
        <div :style="{ width: '100%' }">
          <div v-for="(row, index) in state.scheduleData" :key="index" :class="'timeline title'" :style="{
            height: state.settingData.rowH + 'px', borderRadius: '5px', borderTop: '1px #fff solid',
          }">
            <span>{{ row.title }}</span>
          </div>
        </div>
      </div>
      <div class="sc-main-box" :style="{ width: '90%' }">
        <div class="sc-main-scroll" :style="{ width: state.contentW + 'px' }">
          <div class="sc-main">
            <div v-for="(row, index) in state.totalScheduleData" :key="index" :class="'timeline'"
              :style="{ height: state.settingData.rowH + 'px', display: 'flex', width: '100%' }">
              <unit-div v-for=" n  in  state.unitCnt " :key="'unit' + n" :row-index="index + 7" :key-index="n"
                :row-data="row" :is-business="true" :is-selecting="state.isSelecting"
                :is-selecting-row-index="state.isSelectingRowIndex" :width="state.settingData.unitDivW + 'px'"
                @mouse-down="selectAllStartTime" @mouse-up="selectAllEndTime"></unit-div>
              <reserved-div v-for="( detail, keyNo ) in  row.schedule " :key="'res' + keyNo" :schedule-detail="detail"
                :row-index="7" :key-no="keyNo" :start-text="detail.start" :end-text="detail.end"
                :unit-width="state.settingData.unitDivW" :unit-height="state.settingData.rowH"
                :title-div-width="state.settingData.titleDivW" :border-width="state.settingData.borderW"
                :min-date="state.settingData.startDate" :max-date="state.settingData.endDate"
                :unit="state.settingData.unit" :clear-switch="state.clearSwitch" :is-selecting="state.isSelecting"
                :is-selecting-row-index="state.isSelectingRowIndex" :is-selecting-index="state.isSelectingIndex"
                @delete-schedule-data="deleteScheduleData" @mouse-up="selectEndTime" @click-event="
                  $emit(
                    'click-event',
                  )
                  "></reserved-div>
            </div>
            <div class="timeline" :style="{
              height: state.settingData.timeDivH + 'px',
              color: '#000'
            }">
              <div v-for="n in state.dateCnt * 24" :key="n" class="sc-time" :style="{ width: state.timeDivW + 'px' }">
                {{ getHeaderTime(n - 1) }} : 00
              </div>
            </div>
            <div>
              <div v-for="(row, index) in  state.scheduleData " :key="index" :class="'timeline'"
                :style="{ height: state.settingData.rowH + 'px', display: 'flex', width: '100%' }">
                <unit-div v-for=" n  in  state.unitCnt " :key="'unit' + n" :row-index="index" :key-index="n"
                  :row-data="row" :is-business="isBusiness(index, n - 1)" :is-selecting="state.isSelecting"
                  :is-selecting-row-index="state.isSelectingRowIndex" :width="state.settingData.unitDivW + 'px'"
                  @mouse-down="selectStartTime" @mouse-up="selectEndTime"></unit-div>
                <reserved-div v-for="( detail, keyNo ) in  row.schedule " :key="'res' + keyNo" :schedule-detail="detail"
                  :row-index="index" :key-no="keyNo" :start-text="detail.start" :end-text="detail.end"
                  :unit-width="state.settingData.unitDivW" :unit-height="state.settingData.rowH"
                  :title-div-width="state.settingData.titleDivW" :border-width="state.settingData.borderW"
                  :min-date="state.settingData.startDate" :max-date="state.settingData.endDate"
                  :unit="state.settingData.unit" :clear-switch="state.clearSwitch" :is-selecting="state.isSelecting"
                  :is-selecting-row-index="state.isSelectingRowIndex" :is-selecting-index="state.isSelectingIndex"
                  @delete-schedule-data="deleteScheduleData" @mouse-up="selectEndTime" @click-event="
                    $emit(
                      'click-event',
                    )
                    "></reserved-div>
              </div>
              <div class="finish-btn-container">
                <input type="button" class="btn btn-primary" value="Finish" @click="finishEvent">
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="sc-rows"
        :style="{ width: '3%', height: state.contentH + 'px', background: '#1eaeff', borderRadius: '5px' }">
        <div
          :style="{ width: '100%', height: '20px', color: '#000', padding: '25px 5px', borderBottom: '2px solid #efefef' }">
          <span>day off</span>
        </div>
        <div :style="{ width: '100%' }">
          <div v-for="( row, index ) in  state.scheduleData " :key="index" :class="'timeline title'" :style="{
            height: state.settingData.rowH + 'px', border: 'none', paddingBottom: '2px', background: '#1eaeff', borderRadius: '5px'
          }
            ">
            <span>
              <input type="checkbox" :id="'checkbox-' + index" :checked="selectedRows.includes(index)"
                @change="handleCheckboxChange(index)">
            </span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
/* eslint-disable */
import { defineComponent, reactive } from "vue";
import UnitDiv from "./unitDiv.vue";
import ReservedDiv from "./reservedDiv.vue";

export default defineComponent({
  name: "vue3-scheduler-lite",
  components: {
    "unit-div": UnitDiv,
    "reserved-div": ReservedDiv,
  },
  props: {
    scheduleData: {
      type: Array,
    },
    setting: {
      type: Object,
    },
  },
  data() {
    return {
      selectedRows: []
    }
  },
  methods: {
    handleCheckboxChange(index) {
      if (this.selectedRows.includes(index)) {
        const indexToRemove = this.selectedRows.indexOf(index);
        this.selectedRows.splice(indexToRemove, 1);
        this.state.scheduleData[index].noBusinessDate = false;
      } else {
        this.selectedRows.push(index);
        this.state.scheduleData[index].schedule.length = 0;
        this.state.scheduleData[index].noBusinessDate = true;

      }
    }
  },
  setup(props, { emit }) {
    const today = new Date();
    const year = today.getFullYear();
    const month = (today.getMonth() + 1).toString().padStart(2, '0');
    const day = today.getDate().toString().padStart(2, '0');
    const formatedtoday = `${year}/${month}/${day}`;
    const formatter = new Intl.DateTimeFormat(props.setting.locale, { weekday: 'long' });
    const dayNames = Array.from({ length: 7 }, (_, i) => formatter.format(new Date(0, 0, i)));
    const state = reactive({
      settingData: {
        startDate: formatedtoday,
        endDate: formatedtoday,
        weekdayText: dayNames,
        unit: 60,
        locale: 'en-GB',
        dateDivH: 25,
        borderW: 1, // Px 
        timeDivH: 24, // Px
        unitDivW: (1.107 * props.setting.unit), // Px
        titleDivW: 7, // Percent
        rowH: 45, // Px
      },
      scheduleData: [],
      resultData: {},
      totalScheduleData: [],
      padding: 0,
      timeDivW: 0,
      dateDivW: 0,
      contentH: 0,
      contentW: 0,
      dateCnt: 0,
      unitCnt: 0,
      isSelecting: false,
      isSelectingRowIndex: null,
      isSelectingIndex: null,
      clearSwitch: false,
      isActive: false
    });

    state.resultData = dayNames.reduce((acc, day) => {
      acc[day] = [];
      return acc;
    }, {});
    [0, 1, 2, 3, 4, 5, 6].forEach(index => {
      state.scheduleData.push({
        title: state.settingData.weekdayText[index],
        noBusinessDate: false,
        schedule: [],
      })
    });

    state.totalScheduleData[0] = {
      title: 'All',
      noBusinessDate: false,
      schedule: []
    }
    var i = 0;
    for (const sc in props.scheduleData) {
      props.scheduleData[sc].forEach((item, index) => {
        state.scheduleData.forEach(sc_arr => {
          if (sc == sc_arr.title) {
            var end_str = "";
            var end_hour = parseInt(item.split(":")[0]);
            var end_minute = parseInt(item.split(":")[1]) + props.setting.unit;
            if (parseInt(end_minute) > 59) {
              end_hour++;
              end_minute = end_minute % 60;
            }
            end_str = end_hour + ":" + end_minute;
            state.scheduleData[i].schedule.push({ start: formatedtoday + " " + item, end: formatedtoday + " " + end_str });
          }
        })

      })
      i++;
    }

    const getHeaderTime = (n) => {
      return n % 24;
    };

    const datetimeFormatter = (dateObj) => {
      let year = dateObj.getFullYear();
      let month = dateObj.getMonth() + 1;
      if (month < 10) {
        month = "0" + month;
      }
      let date = dateObj.getDate();
      let hours = dateObj.getHours();
      if (hours < 10) {
        hours = "0" + hours;
      }
      let minutes = dateObj.getMinutes();
      if (minutes < 10) {
        minutes = "0" + minutes;
      }
      return year + "/" + month + "/" + date + " " + hours + ":" + minutes;
    };

    const addMinutes = (dateObj, n) => {
      dateObj.setTime(dateObj.getTime() + n * 60 * 1000);
      return dateObj;
    };

    const isBusiness = (rowIndex, n) => {
      let noBusinessDate = state.scheduleData[rowIndex].noBusinessDate;
      if (noBusinessDate) {
        return false;
      } else {
        return true;
      }
    };

    const selectStartTime = (rowIndex, keyIndex) => {

      state.isSelecting = true;
      state.isSelectingRowIndex = rowIndex;
      let addMinutes1 = (keyIndex - 1) * state.settingData.unit;
      let addMinutes2 = keyIndex * state.settingData.unit;
      let newStartDateObj = addMinutes(
        new Date(state.settingData.startDate),
        addMinutes1
      );
      let newEndDateObj = addMinutes(
        new Date(state.settingData.startDate),
        addMinutes2
      );
      var k = [];
      state.scheduleData[rowIndex].schedule.forEach(item => {
        if (item.start == datetimeFormatter(newStartDateObj) || state.scheduleData[rowIndex].noBusinessDate == true) {
          k.push(rowIndex);
        }
      })

      if (!k.includes(rowIndex) && state.scheduleData[rowIndex].noBusinessDate == false) {
        state.scheduleData[rowIndex].schedule.push({
          start: datetimeFormatter(newStartDateObj),
          end: datetimeFormatter(newEndDateObj),
        });
      }
      state.isSelectingIndex =
        state.scheduleData[state.isSelectingRowIndex].schedule.length - 1;
    };

    const selectEndTime = (startDate) => {
      if (state.isSelecting) {
        if (startDate == undefined) {
          let targetData =
            state.scheduleData[state.isSelectingRowIndex].schedule[
            state.scheduleData[state.isSelectingRowIndex].schedule.length - 1
            ];
          startDate = targetData.start;
          endDate = targetData.end;
        }
      }
      state.isSelecting = false;
      state.isSelectingRowIndex = null;
      state.isSelectingIndex = null;
      state.clearSwitch = !state.clearSwitch;
    };

    const selectAllStartTime = (rowIndex, keyIndex) => {

      state.isSelecting = true;
      state.isSelectingRowIndex = 0;
      let addMinutes1 = (keyIndex - 1) * state.settingData.unit;
      let addMinutes2 = keyIndex * state.settingData.unit;
      let newStartDateObj = addMinutes(
        new Date(state.settingData.startDate),
        addMinutes1
      );
      let newEndDateObj = addMinutes(
        new Date(state.settingData.startDate),
        addMinutes2
      );

      state.totalScheduleData[0].schedule.push({
        start: datetimeFormatter(newStartDateObj),
        end: datetimeFormatter(newEndDateObj),
      });

      state.isSelectingIndex =
        state.scheduleData[state.isSelectingRowIndex].schedule.length - 1;

      [0, 1, 2, 3, 4, 5, 6].forEach(row => {
        selectStartTime(row, keyIndex);
      });


    };

    const selectAllEndTime = (startDate) => {
      if (state.isSelecting) {
        if (startDate == undefined) {
          let targetData =
            state.totalScheduleData[state.isSelectingRowIndex].schedule[
            state.totalScheduleData[state.isSelectingRowIndex].schedule.length - 1
            ];
          startDate = targetData.start;
          endDate = targetData.end;
        }
      }
      state.isSelecting = false;
      state.isSelectingRowIndex = null;
      state.isSelectingIndex = null;
      state.clearSwitch = !state.clearSwitch;
    };
    const deleteScheduleData = (rowIndex, keyNo) => {

      if (rowIndex == 7) {
        deleteAllScheduleData(keyNo, state.totalScheduleData[0].schedule[keyNo]);
      } else {

        state.scheduleData[rowIndex].schedule.splice(keyNo, 1);

      }
    };
    const deleteAllScheduleData = (keyNo, key) => {

      [0, 1, 2, 3, 4, 5, 6].forEach(row => {
        state.scheduleData[row].schedule.forEach((item, index) => {

          if (item.start == key.start) {
            deleteScheduleData(row, index);
          }
        })

      });
      state.totalScheduleData[0].schedule.splice(keyNo, 1);
    };


    const getMinutesDiff = (date1, date2) => {
      const diffTime = Math.abs(date2 - date1);
      return Math.ceil(diffTime / (1000 * 60));
    };

    state.settingData = Object.assign(state.settingData, props.setting);
    state.dateCnt = 1;
    let oneDayCnt = parseInt(1440 / state.settingData.unit);
    state.unitCnt = oneDayCnt * state.dateCnt;
    state.padding =
      state.settingData.dateDivH +
      state.settingData.timeDivH +
      state.settingData.borderW * 4;
    state.dateDivW =
      state.settingData.unitDivW * oneDayCnt +
      (oneDayCnt - state.settingData.borderW);
    state.contentH =
      state.padding +
      (state.settingData.rowH + state.settingData.borderW * 2) *
      state.scheduleData.length;
    state.contentW =
      state.dateDivW * state.dateCnt +
      state.dateCnt * state.settingData.borderW;
    state.timeDivW =
      (60 / state.settingData.unit) *
      (state.settingData.unitDivW + state.settingData.borderW) -
      1;

    const toggleView = () => {
      state.isActive = !state.isActive;
    }
    const finishEvent = () => {
      state.scheduleData.forEach((item) => {
        state.resultData[item.title].length = 0;
        item.schedule.forEach(val => {
          state.resultData[item.title].push(val.start.split(" ")[1])
        })
      })
      emit('finish-Event', state.resultData);
    }

    return {
      state,
      getHeaderTime,
      datetimeFormatter,
      addMinutes,
      isBusiness,
      selectStartTime,
      selectEndTime,
      selectAllStartTime,
      selectAllEndTime,
      deleteScheduleData,
      getMinutesDiff,
      toggleView,
      finishEvent
    };
  },
});
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.clear {
  clear: both;
  height: 0;
  line-height: 0;
}

.sc-rows {
  float: left;
  font-weight: bold;
  background: #fff;
  border-color: #c0c0c0;
  color: white;
  position: relative;
}

.sc-rows .title {
  background: #fff;
  color: #000;
  text-align: center;
  border: #000 1px solid;
}

.sc-main-scroll .sc-time {
  color: #000;
  padding: 4px 0;
  line-height: 18px;
  height: 18px;
  display: block;
  float: left;
  border-right: solid 1px #ccc;
  text-align: center;
  font-size: 16px;
}

.sc-main-box {
  float: left;
  overflow-x: auto;
  overflow-y: auto;
}

.sc-main {
  position: relative;
}

.timeline {
  position: relative;

}

.sc-bar {
  position: absolute;
  color: #fff;
  background: #ff4800;
  cursor: pointer;
  z-index: 10;
  box-shadow: 2px 2px 4px #333;
  -moz-box-shadow: 2px 2px 4px #333;
  -webkit-box-shadow: 2px 2px 4px #333;
}

.ui-draggable-dragging,
.ui-resizeable {
  z-index: 20;
}

.timeline,
.sc-main .tb {
  border-bottom: solid 2px #666;
}

.sc-rows .timeline {
  overflow: hidden;
}

.sc-rows .timeline span {
  padding: 10px;
  display: block;
}

.sc-rows .timeline span.photo {
  float: left;
  width: 36px;
  height: 36px;
  padding: 10px 0 10px 10px;
}

.sc-rows .timeline span.title {
  float: left;
  padding: 10px 0 10px 10px;
  width: 92px;
}

.sc-main-scroll .sc-main .tl {
  float: left;
  height: 100%;
  border-right: solid 1px #ccc;
}

.sc-main-scroll .sc-main .tl:hover {
  background: #f0f0f0;
}

.sc-time {
  font-size: 10px;
}

.cant-res {
  background-color: #999 !important;
}

.isMe {
  background-color: #108000 !important;
}

.notMe {
  background-color: #3765e3 !important;
}


.sample {
  width: 10px;
  height: 10px;
  margin: 5px;
  border: 1px solid black;
}

.cant-res {
  background-color: #efefef !important;
}

.reserved {
  background-color: #ec2906 !important;
}

.first-line {
  border: #000 solid 1px;
}

.finish-btn-container {
  margin-top: 10px;
  text-align: center;
}

.day-off-container {
  display: flex;
}
</style>
