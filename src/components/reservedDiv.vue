<template>
  <div v-if="state.isShow" :class="['sc-bar', state.isMe ? 'isMe' : 'notMe']" :style="state.styleObject"
    @mouseup="mouseup" @click="$emit('click-event')">
    <span style="float: right; width: 100%; height: 100%; padding: 0px" @click="deleteEvent"></span>
  </div>
</template>

<script>
import { defineComponent, reactive, watch } from "vue";

export default defineComponent({
  name: "reserved-div",
  props: {
    rowIndex: Number,
    keyNo: Number,
    unitWidth: Number,
    unitHeight: Number,
    titleDivWidth: Number,
    borderWidth: Number,
    startText: String,
    endText: String,
    contentText: String,
    minDate: String,
    maxDate: String,
    unit: Number,
    clearSwitch: Boolean,
    isSelecting: Boolean,
    isSelectingRowIndex: Number,
    isSelectingIndex: Number,
  },
  setup(props, { emit }) {
    const state = reactive({
      styleObject: {
        Opacity: 1,
        left: "0px",
        width: "0px",
        height: "45px",
      },
      isShow: false,
      mouseXStarted: null,
      startLineNo: null,
      endLineNo: null,
    });

    /**
     * Set the Block left pixel
     *
     * @returns void
     */
    const setLeftPosition = () => {
      let leftDiff = getMinutesDiff(
        new Date(props.minDate),
        new Date(props.startText)
      );
      let shiftCnt = parseInt(leftDiff / props.unit);
      state.startLineNo = shiftCnt;
      let shiftLeft = props.unitWidth * shiftCnt + shiftCnt * props.borderWidth;
      state.styleObject.left = shiftLeft + "px";
    };
    /**
     * Set the Block width pixel
     *
     * @returns void
     */
    const setWidth = () => {
      let rightDiff = getMinutesDiff(
        new Date(props.startText),
        new Date(props.endText)
      );
      let widthCnt = parseInt(rightDiff / props.unit);
      state.endLineNo = state.startLineNo + widthCnt;
      let width = props.unitWidth * widthCnt + widthCnt * props.borderWidth;
      state.styleObject.width = width + "px";
    };




    /**
     * Delete this event
     */
    const deleteEvent = () => {
      emit("delete-schedule-data", props.rowIndex, props.keyNo);
    };
    /**
     * Mouse move event for Add new schedule
     */
    const mousemove = (e) => {
      if (
        props.rowIndex == props.isSelectingRowIndex &&
        props.keyNo == props.isSelectingIndex
      ) {
        if (props.isSelecting && state.mouseXStarted) {
          let movePx = e.clientX - state.mouseXStarted;
          let unitCnt = parseInt(movePx / props.unitWidth);
          if (unitCnt != 0 && unitCnt < 0) {
            state.mouseXStarted = e.clientX + props.unitWidth;
            emit("edit-schedule-data", props.rowIndex, props.keyNo, unitCnt);
          }
        }
        if (props.isSelecting && !state.mouseXStarted) {
          state.mouseXStarted = e.clientX;
          state.styleObject.Opacity = 0.5;
        }
      }
    };
    /**
     * Mouse up event for Add new schedule
     */
    const mouseup = () => {
      emit("mouse-up", props.startText, props.endText);
    };
    /**
     * Get minutes diff between date1 and date2
     *
     * @param Object date1 DateObject1
     * @param Object date2 DateObject2
     *
     * @returns Int
     */
    const getMinutesDiff = (date1, date2) => {
      const diffTime = date2 - date1;
      return Math.ceil(diffTime / (1000 * 60));
    };

    watch(
      () => props.startText,
      (newVal, oldVal) => {
        if (newVal != oldVal) {
          setLeftPosition();
        }
      }
    );

    watch(
      () => props.endText,
      (newVal, oldVal) => {
        if (newVal != oldVal) {
          setWidth();
          if (
            props.isSelecting &&
            state.mouseXStarted &&
            props.rowIndex == props.isSelectingRowIndex &&
            props.keyNo == props.isSelectingIndex
          ) {
            let diff = getMinutesDiff(new Date(oldVal), new Date(newVal));
            let cnt = parseInt(diff / props.unit);
            state.mouseXStarted += props.unitWidth * cnt;
          }
        }
      }
    );



    setLeftPosition();
    setWidth();
    state.isShow = true;
    return {
      state,
      setLeftPosition,
      setWidth,
      deleteEvent,
      mousemove,
      mouseup,
      getMinutesDiff,
    };
  },
});
</script>

<style scoped></style>