<template>
  <div class="widgetsBar">
    <!-- <div class="widgetItem" id="circle" @mousedown="widgetItemMouseDown"></div> -->

    <widgetItem v-for="widget in widgets" :key="widget.id" :widget="widget" @itemClick="itemClick"></widgetItem>
  </div>
</template>

<style>
.widgetsBar {
  width: 50px;
  position: absolute;
  top: 20px;
}
</style>

<script>
import widgetItem from "./WidgetItem";
export default {
  props: ["widgets"],
  data() {
    return {};
  },
  methods: {
    itemClick(widget) {
      this.$emit("itemClickMouseDown", widget);
    }
  },
  components: {
    widgetItem
  }
};
</script>