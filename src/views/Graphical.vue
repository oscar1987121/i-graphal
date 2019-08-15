<template>
  <div class="graphical-view">
    <widgetBar :widgets="widgetFeatures" @itemClickMouseDown="itemClickMouseDown"></widgetBar>
    <div
      class="control-group"
      @mouseup="controlSelectUp"
      @mousedown="controlSelectDown"
      @mousemove="widgetItemMouseMove"
      v-show="dragShape !== null"
    >
      <div class="cp ctl"></div>
      <div class="cp ctc"></div>
      <div class="cp ctr"></div>
      <div class="cp ccl"></div>
      <div class="cp ccr"></div>
      <div class="cp cbl"></div>
      <div class="cp cbc"></div>
      <div class="cp cbr"></div>
    </div>
  </div>
</template>
<style>
.graphical-view {
  width: 100%;
  height: 100%;
}

.cp {
  width: 6px;
  height: 6px;
  background-color: blue;
  position: absolute;
  float: left;
}
.control-group {
  position: absolute;
  border: 1px solid red;
  box-sizing: border-box;
  z-index: 2;
}

.canvas {
  width: 100%;
  height: 100%;
  background-color: burlywood;
}

.shape {
  width: 50px;
  height: 50px;
  border: 1px solid rgb(185, 185, 185);
  box-sizing: border-box;
  background-color: white;
}

.widgetGroup {
  /* border: 1px solid blue; */
  position: absolute;
}
</style>
<script>
import widgetBar from "@/components/widget/WidgetBar";
import widgetTool from "@/components/widget/widgetTool";

export default {
  mixins: [widgetTool],
  data() {
    return {
      dragShape: null,
      mouseDown: false,
      targetX: 0,
      targetY: 0,
      index: 0,
      selectWidget: null
    };
  },
  watch: {
    dragShape(val) {
      if (val) {
        this.changeControlBar(val);
      }
    }
  },

  mounted() {
    document.body.addEventListener("mousemove", this.widgetItemMouseMove);
    document.body.addEventListener("mouseup", this.widgetItemMouseUp);
  },
  components: {
    widgetBar
  },
  methods: {
    //control-group mouse down
    controlSelectDown() {
      this.mouseDown = true; //mark the mouse down action
      let currentShapeId = $("div.control-group").attr("currentShapeId"); //read the control-group attr 'id'
      this.dragShape = $(`div#${currentShapeId}`); //find the element by 'id'

      //get the mouse position
      let mouseX = event.clientX;
      let mouseY = event.clientY;

      //mark the gap between the mouse position and the position of the shape
      this.mouseShapeGapX = mouseX - this.dragShape.offset().left;
      this.mouseShapeGapY = mouseY - this.dragShape.offset().top;
    },
    //control-group mouse up
    controlSelectUp() {
      this.mouseDown = false;
    },
    //alter the control-group position
    changeControlBar(val) {
      //target position

      let targetX = val.position().left;
      let targetY = val.position().top;

      //target size
      let targetW = val.width();
      let targetH = val.height();

      //control group element
      var controlGroup = $("div.control-group");

      //change control group attr 'id' to target 'id'
      controlGroup.attr("currentShapeId", val.attr("id"));

      //change the control group position and size to fit the target
      controlGroup.css({
        top: targetY + "px",
        left: targetX + "px",
        width: targetW + "px",
        height: targetH + "px"
      });

      //get all the cp(control point)
      let cpList = controlGroup.children(".cp");
      for (let i = 0; i < cpList.length; i++) {
        //get the cp position
        let cp = cpList.get(i);
        let cpX = cp.offsetLeft;
        let cpY = cp.offsetTop;

        //top left
        if (cp.className.includes("ctl")) {
          cp.style.left = 0 - 3 + "px";
          cp.style.top = 0 - 3 + "px";
        }
        //top center
        else if (cp.className.includes("ctc")) {
          cp.style.left = "50%";
          cp.style.marginLeft = 0 - 3 + "px";
          cp.style.top = 0 - 3 + "px";
        }
        //top right
        else if (cp.className.includes("ctr")) {
          cp.style.left = "100%";
          cp.style.marginLeft = 0 - 3 + "px";
          cp.style.top = 0 - 3 + "px";
        }
        //center left
        else if (cp.className.includes("ccl")) {
          cp.style.top = "50%";
          cp.style.marginTop = 0 - 3 + "px";

          cp.style.left = 0 - 3 + "px";
        }
        //center right
        else if (cp.className.includes("ccr")) {
          cp.style.top = "50%";
          cp.style.marginTop = 0 - 3 + "px";

          cp.style.left = "100%";
          cp.style.marginLeft = 0 - 3 + "px";
        }
        //bottom left
        else if (cp.className.includes("cbl")) {
          cp.style.top = "100%";
          cp.style.marginTop = 0 - 3 + "px";

          cp.style.left = 0 - 3 + "px";
        }
        //bottom center
        else if (cp.className.includes("cbc")) {
          cp.style.top = "100%";
          cp.style.marginTop = 0 - 3 + "px";

          cp.style.left = "50%";
          cp.style.marginLeft = 0 - 3 + "px";
        }
        //bottom right
        else if (cp.className.includes("cbr")) {
          cp.style.top = "100%";
          cp.style.marginTop = 0 - 3 + "px";

          cp.style.left = "100%";
          cp.style.marginLeft = 0 - 3 + "px";
        }
      }
    },
    //tool bar widget mouse down
    itemClickMouseDown(widget) {
      this.mouseDown = true; //点下标识
      this.dragShape = null; //被移动
      this.selectWidget = widget;
    },
    //tool bar widget mouse up
    widgetItemMouseUp() {
      this.mouseDown = false;
      // this.dragShape = null;
      this.selectWidget = null;
    },
    //mouse move
    widgetItemMouseMove() {
      //do nothing if mouse is not in the down status
      if (!this.mouseDown) {
        return;
      }

      let view = $(".graphical-view");
      //get the mouse absolute position = mouse position - current view position
      let mouseX = event.clientX - view.offset().left;
      let mouseY = event.clientY - view.offset().top;

      let self = this;
      // console.log(`mouseX = ${mouseX},mouseY = ${mouseY}`);
      //create one new shape when it is not any shape element selection
      if (this.dragShape == null) {
        //create widget uuid
        let widgetId = this.index++;

        //create a shape group element in view

        let groupElement = $(`<div id=${widgetId} class='widgetGroup'></div>`);
        view.append(groupElement);

        this.dragShape = groupElement;
        groupElement.css({ top: `${mouseY}px`, left: `${mouseX}px` });

        let graphElement = $("<div class='shape'></div>");
        groupElement.append(graphElement);
        graphElement.css(this.selectWidget.widget.style);

        //the default gap between mouse and shape is 0
        this.mouseShapeGapX = 0;
        this.mouseShapeGapY = 0;

        //add a shape group select listener
        groupElement.on("mousedown", function() {
          //mark down the mouse down record
          self.mouseDown = true;

          //mark the current select shape
          self.dragShape = $(event.currentTarget);

          //get the mouse position
          let mouseX = event.clientX;
          let mouseY = event.clientY;
          //cal the gap between mouse and shape
          self.mouseShapeGapX = mouseX - self.dragShape.offset().left;
          self.mouseShapeGapY = mouseY - self.dragShape.offset().top;
        });

        //add a shape group mouse up listener
        groupElement.on("mouseup", function() {
          self.mouseDown = false;
        });

        //when create completed, clean the widget tool select target
        this.selectWidget = null;
      }
      //move the shape if is not tool selection
      else {
        //cal the new position of the shape
        let x = mouseX - this.mouseShapeGapX;
        let y = mouseY - this.mouseShapeGapY;

        //can not over the current view
        if (x <= 0) {
          x = 0;
        }
        if (y <= 0) {
          y = 0;
        }

        this.dragShape.css({ left: `${x}px`, top: `${y}px` });
        //change the control-group position
        this.changeControlBar(this.dragShape);
      }
    }
  }
};
</script>