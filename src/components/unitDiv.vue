<template>
  <div :class="['tl', isBusiness ? 'can-res' : 'cant-res']" :style="{ width: width }" @mousedown="mousedown"
    @mouseenter="mouseenter">
  </div>
</template>

<script>
import { defineComponent } from "vue";

export default defineComponent({
  name: "unit-div",
  props: {
    rowIndex: Number,
    keyIndex: Number,
    width: String,
    rowData: Object,
    isBusiness: Boolean,
    isSelecting: Boolean,
    isSelectingRowIndex: Number,
  },
  setup(props, { emit }) {
    const mousedown = () => {
      if (!props.isBusiness) {
        return false;
      }
      emit("mouse-down", props.rowIndex, props.keyIndex);
    };
    const mouseenter = () => {
      if (
        !props.isSelecting ||
        props.rowIndex != props.isSelectingRowIndex ||
        !props.isBusiness
      ) {
        return false;
      }
      emit("mouse-enter", props.keyIndex);
    };
    const mouseup = () => {
      emit("mouse-up");
    };

    return {
      mousedown,
      mouseenter,
      mouseup,
    };
  },
});
</script>

<style scoped></style>