<template>
  <div class="home">
    <div id="selectDiv"></div>
    <canvas id="myCanvas"></canvas>
    <div>
      <button @click="screenShot">选取截图</button>
      <button @click="screenAll">整屏截图</button>
    </div>
    <img alt="Vue logo" src="../assets/logo.png" />
    <img alt="Vue logo" src="../assets/logo.png" />
    <img alt="Vue logo" src="../assets/logo.png" />
    <img alt="Vue logo" src="../assets/logo.png" />
    <img alt="Vue logo" src="../assets/logo.png" />
  </div>
</template>

<script>
import html2canvas from "html2canvas";
import canvasToImage from "canvas2image";

export default {
  name: "Home",
  data() {
    return {
      cutFlag: false,
      myx: 0,
      myy: 0,
    };
  },
  components: {},
  mounted: function () {},
  methods: {
    screenAll() {
      var test = document.querySelector("body");
      //将jQuery对象转换为dom对象
      html2canvas(test).then(function (canvas) {
        test.appendChild(canvas);
      });
    },
    screenShot() {
      let _this = this;
      this.cutFlag = true;
      var img;
      var test = document.querySelector("body");
      html2canvas(test)
        .then((canvas) => {
          this.$("html").css("cursor", "crosshair");
          img = canvasToImage.convertToImage(
            canvas,
            canvas.width,
            canvas.height
          );
          var wId = "w";
          var index = 0;
          var startX = 0,
            startY = 0;
          var flag = false;
          var retcLeft = "0px",
            retcTop = "0px",
            retcHeight = "0px",
            retcWidth = "0px";
          var canvasLeft = 0,
            canvasTop = 0,
            canvasHeight = 0,
            canvasWidth = 0;
          document.onmousedown = function (e) {
            if (!flag && _this.cutFlag) {
              flag = true;
              try {
                var evt = window.event || e;
                var scrollTop =
                  document.body.scrollTop || document.documentElement.scrollTop;
                var scrollLeft =
                  document.body.scrollLeft ||
                  document.documentElement.scrollLeft;
                startX = evt.clientX + scrollLeft;
                startY = evt.clientY + scrollTop;
                _this.$("#selectDiv").css("top", startY);
                _this.$("#selectDiv").css("left", startX);
                this.myx = startX;
                this.myy = startY;
                index++;
                var div = document.createElement("div");
                div.id = wId + index;
                div.className = "div";
                div.style.marginLeft = startX + "px";
                div.style.marginTop = startY + "px";
                document.body.appendChild(div);
              } catch (e) {
                console.log(e);
              }
            } else {
              flag = false;
            }
          };
          document.onmouseup = (e) => {
            this.$("html").css("cursor", "default");
            if (flag && _this.cutFlag) {
              _this.$("#selectDiv").css("top", "-10px");
              _this.$("#selectDiv").css("left", "-10px");
              _this.$("#selectDiv").css("width", "0px");
              _this.$("#selectDiv").css("heigh", "0px");

              try {
                var evt = window.event || e;
                if (startX !== evt.clientX || startY !== evt.clientY) {
                  _this.cutFlag = false;
                  var arr = document.getElementsByClassName("div");
                  for (let i = 0; i < arr.length; i++) {
                    if (arr[i] != null) {
                      arr[i].parentNode.removeChild(arr[i]);
                    }
                  }
                  var retCanvas = document.createElement("canvas");
                  var retCtx = retCanvas.getContext("2d");
                  retCanvas.width = canvasWidth;
                  retCanvas.height = canvasHeight;
                  retCanvas.style.background = "#ffffff";
                  retCtx.drawImage(
                    img,
                    canvasLeft,
                    canvasTop,
                    canvasWidth,
                    canvasHeight,
                    0,
                    0,
                    canvasWidth,
                    canvasHeight
                  );
                  var img2 = canvasToImage.convertToImage(
                    retCanvas,
                    canvasWidth,
                    canvasHeight
                  );
                  _this.confirm(_this.convertBase64UrlToBlob(img2.src));
                  var canvas2 = document.querySelector("#myCanvas");
                  canvas2.remove();
                } else {
                  _this.cutFlag = false;
                  let arr = document.getElementsByClassName("div");
                  for (let i = 0; i < arr.length; i++) {
                    if (arr[i] != null) {
                      arr[i].parentNode.removeChild(arr[i]);
                    }
                  }
                  if (document.querySelector("#myCanvas")) {
                    let canvas2 = document.querySelector("#myCanvas");
                    canvas2.remove();
                  }
                }
              } catch (e) {
                console.log(e);
              }
            } else {
              let arr = document.getElementsByClassName("div");
              for (let i = 0; i < arr.length; i++) {
                if (arr[i] != null) {
                  arr[i].parentNode.removeChild(arr[i]);
                }
              }
              if (document.querySelector("#myCanvas")) {
                let canvas2 = document.querySelector("#myCanvas");
                canvas2.remove();
              }
            }
          };
          document.onmousemove = function (e) {
            if (flag && _this.cutFlag) {
              try {
                var evt = window.event || e;

                var scrollTop =
                  document.body.scrollTop || document.documentElement.scrollTop;
                var scrollLeft =
                  document.body.scrollLeft ||
                  document.documentElement.scrollLeft;
                retcLeft =
                  (startX - evt.clientX - scrollLeft > 0
                    ? evt.clientX + scrollLeft
                    : startX) + "px";
                canvasLeft =
                  startX - evt.clientX - scrollLeft > 0
                    ? evt.clientX + scrollLeft
                    : startX;
                retcTop =
                  (startY - evt.clientY - scrollTop > 0
                    ? evt.clientY + scrollTop
                    : startY) + "px";
                canvasTop =
                  startY - evt.clientY - scrollTop > 0
                    ? evt.clientY + scrollTop
                    : startY;
                retcHeight = Math.abs(startY - evt.clientY - scrollTop) + "px";
                canvasHeight = Math.abs(startY - evt.clientY - scrollTop);
                retcWidth = Math.abs(startX - evt.clientX - scrollLeft) + "px";
                canvasWidth = Math.abs(startX - evt.clientX - scrollLeft);

                if (startY - evt.clientY - scrollTop > 0) {
                  _this.$("#selectDiv").css("top", evt.clientY + "px");
                }
                if (startX - evt.clientX - scrollLeft > 0) {
                  _this.$("#selectDiv").css("left", evt.clientX + "px");
                }
                _this.$("#selectDiv").css("width", retcWidth);
                _this.$("#selectDiv").css("height", retcHeight);
                $(wId + index).style.marginLeft = retcLeft;
                $(wId + index).style.marginTop = retcTop;
                if (evt.clientX > window.innerWidth) {
                  $(wId + index).style.width = window.innerWidth - retcLeft;
                } else {
                  $(wId + index).style.width = retcWidth;
                }
                if (evt.clientY > window.innerHeight) {
                  $(wId + index).style.height = window.innerHeight - retcTop;
                } else {
                  $(wId + index).style.height = retcHeight;
                }
              } catch (e) {
                console.log(e);
              }
            }
          };
          var $ = function (id) {
            return document.getElementById(id);
          };
        })
        .catch({});
    },
    //将base64图片转为blob格式
    convertBase64UrlToBlob(dataurl) {
      console.log(dataurl);
      try {
        var arr = dataurl.split(","),
          // mime = arr[0].match(/:(.*?);/)[1],
          bstr = atob(arr[1]),
          n = bstr.length,
          u8arr = new Uint8Array(n);
        while (n--) {
          u8arr[n] = bstr.charCodeAt(n);
        }
        return new Blob([u8arr], {
          type: "image/png",
        });
      } catch (err) {
        console.log(err.message);
      }
    },
    //图片发送
    confirm(file) {
      console.log(file);
      // this.upload(file);
    },
  },
};
</script>
<style lang="less" scoped>
#myCanvas {
  position: absolute;
  top: 0;
  left: 0;
  z-index: 9998;
  overflow: hidden;
}
#selectDiv {
  position: absolute;
  left: -10px;
  top: -10px;
  border: 2px dashed black;
  background: aqua;
  opacity: 0.3;
}
</style>
