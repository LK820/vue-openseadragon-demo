<template>
    <div style="height: 100%">
        <div id="openSeaDragonDiv" style="height: 90%">

        </div>
        <button @click="activeBrush">画笔</button>
    </div>
</template>

<script>
  export default {
    name: "Demo.vue",
    data() {
      return {
        viewer: null,
        options: {
          id: "openSeaDragonDiv",
          prefixUrl: "https://openseadragon.github.io/openseadragon/images/",
          constrainDuringPan: true,
          showNavigator: true,
        },
        brushState: true, //是否打开画笔
        hitItem: null,
        path: null,
      }
    },
    methods: {
      activeBrush() {
        this.brushState = true
        this.setMouseNavEnabled(false)
      },
      pressHandler(event) {
        this.hitItem = null;
        var transformed_point = paper.view.viewToProject(new paper.Point(event.position.x, event.position.y));
        var hit_test_result = paper.project.hitTest(transformed_point);
        if (hit_test_result && !this.brushState) {
          this.hitItem = hit_test_result.item;
        }

        if (this.brushState) {
          this.path = new paper.Path();
          this.path.strokeColor = 'red';
          this.path.strokeWidth = 50;
          this.path.fillColor = "rgba(100,150,185,0)";
          var imagePoint = this.toImagePoint(event)
          this.path.add(new paper.Point(imagePoint.x, imagePoint.y));
        }
      },
      dragHandler(event) {
        if (this.hitItem && !this.brushState) {
          var transformed_point1 = paper.view.viewToProject(new paper.Point(0, 0));
          var transformed_point2 = paper.view.viewToProject(new paper.Point(event.delta.x, event.delta.y));
          this.hitItem.position = this.hitItem.position.add(transformed_point2.subtract(transformed_point1));
          this.setMouseNavEnabled(false);
          paper.view.draw();

        }
        if (this.brushState) {
          var imagePoint = this.toImagePoint(event)
          this.path.add(new paper.Point(imagePoint.x, imagePoint.y));
          paper.view.draw();
        }
      },
      dragEndHandler(event) {
        if (this.hitItem && !this.brushState) {
          this.setMouseNavEnabled(true);
        }
        if (this.brushState) {
          this.path = null;
          this.setMouseNavEnabled(true);
        }
        this.brushState = false;
        this.hitItem = null;
      },
      toImagePoint(event){
        var webPoint = event.position;
        var viewportPoint = this.viewer.viewport.pointFromPixel(webPoint);
        return this.viewer.viewport.viewportToImageCoordinates(viewportPoint);
      },
      setMouseNavEnabled(val) {
        this.viewer.setMouseNavEnabled(val)
      }
    },
    created() {
    },
    mounted() {
      this.viewer = OpenSeadragon(this.options);
      this.viewer.addTiledImage({
        tileSource: "http://openseadragon.github.io/example-images/highsmith/highsmith.dzi",
      });

      var overlay = this.viewer.paperjsOverlay();
      new OpenSeadragon.MouseTracker({
        element: this.viewer.canvas,
        pressHandler: this.pressHandler,
        dragHandler: this.dragHandler,
        dragEndHandler: this.dragEndHandler
      }).setTracking(true);

      window.onresize = () => {
        overlay.resize();
        overlay.resizecanvas();
        
      }
    }
  }
</script>
