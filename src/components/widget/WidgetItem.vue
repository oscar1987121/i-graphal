<template>
  <div class="widgetItem" id="widget.id" @mousedown="widgetItemMouseDown">
      <i :class="widget.icon"></i>
  </div>
</template>

<style>
.widgetItem {
  width: 100%;
  height: 50px;
  background-color: beige;
  text-align: center;
  line-height: 50px;
}
</style>

<script>
export default {
  props: ["widget"],
  data() {
    return {};
  },
  methods: {
    widgetItemMouseDown() {
      this.$emit("itemClick", this);
    }
  }
};
</script>