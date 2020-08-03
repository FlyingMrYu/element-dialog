<template>
  <transition name="fade-in-linear">
    <div v-if="visible" class="my_dialog">
      <!-- 遮罩层 -->
      <div class="my_dialog_mask"></div>
      <div
        v-drag="dragDown"
        :style="{top:topMover,left:leftMover,width:width,backgroundColor:backgroundColor,height:height,minWidth:minWidth}"
        class="my_dialog_box"
        id="my_dialog_box"
      >
        <!-- 标题 -->
        <!-- <div class="my_dialog_title">
          {{title}}
          <span class="my_dialog_close" @click="cancel">
            <img :src="colseImage" />
          </span>
        </div>-->
        <!-- 内容 -->
        <div class="my_dialog_content">
          <span class="my_dialog_close" :style="{right:right}" @click="cancel">
            <img :src="colseImage" />
          </span>
          <slot></slot>
        </div>
        <!-- 底部按钮 -->
        <!-- <div class="my_dialog_bottom" v-if="confirmtext && canceltext">
          <button class="btn cancelBtn" @click="cancel">{{canceltext}}</button>
          <button class="btn confirmBtn" @click="confirm">{{confirmtext}}</button>
        </div>-->
      </div>
    </div>
  </transition>
</template>
<script>
import colseImage from "@/assets/img/home/close.png";
export default {
  name: "Dialog",
  props: {
    visible: {
      type: Boolean,
      default: false
    },
    width: {
      require: true
    },
    backgroundColor: {
      default: ""
    },
    title: {
      type: String,
      default: "fdsa"
    },
    minWidth: {
      default: "700px"
    },
    right:{
      default: "20px"
    },
    height: {
      default: "100%"
    },
    confirmtext: {
      type: String,
      default: ""
    },
    cancelDialog: {
      type: Function
    },
    canceltext: {
      type: String,
      default: ""
    },
    topMover: {
      default: "50%"
    },
    leftMover: {
      default: "50%"
    },
    dragDown:{
       default: false
    }
  },
  data() {
    return {
      colseImage: colseImage
    };
  },
  directives: {
    drag: {
      inserted: function(el, binding, vnode) {
        if(binding.value) return;
        vnode = vnode.elm;
        el.onmousedown = event => {
          event.stopImmediatePropagation();
          // if (event.target.className !== "my_dialog_title") {
          //   return;
          // }
          // (clientX, clientY)点击位置距离当前可视区域的坐标(x，y)
          // offsetLeft, offsetTop 距离上层或父级的左边距和上边距

          // 获取鼠标在弹窗中的位置
          let mouseX = event.clientX - vnode.offsetLeft;
          let mouseY = event.clientY - vnode.offsetTop;

          // 绑定移动和停止函数
          document.onmousemove = event => {
            let left, top;

            // 获取新的鼠标位置(event.clientX, event.clientY)
            // 弹窗应该在的位置(left, top)
            left = event.clientX - mouseX;
            top = event.clientY - mouseY;

            // offsetWidth、offsetHeight 当前元素的宽度
            // innerWidth、innerHeight 浏览器可视区的宽度和高度

            // 获取弹窗在页面中距X轴的最小、最大 位置
            let minX = -vnode.offsetWidth / 2 + 100;
            let maxX = window.innerWidth + vnode.offsetWidth / 2 - 100;
            if (left <= minX) {
              left = minX;
            } else if (left >= maxX) {
              left = maxX;
            }

            // 获取弹窗在页面中距Y轴的最小、最大 位置
            let minY = vnode.offsetHeight / 2;
            let maxY = window.innerHeight + vnode.offsetHeight / 2 - 100;
            if (top <= minY) {
              top = minY;
            } else if (top >= maxY) {
              top = maxY;
            }
            // 赋值移动
            vnode.style.left = left + "px";
            vnode.style.top = top + "px";
          };
          document.onmouseup = () => {
            document.onmousemove = document.onmouseup = null;
          };
        };
        window.onresize = () => {
          vnode.style.left = "50%";
          vnode.style.top = "50%";
        };
      }
    }
  },
  methods: {
    cancel() {
      this.$emit("cancelDialog");
    },
    confirm() {
      this.$emit("confirm");
    }
  }
};
</script>
<style scoped lang="scss">
.my_dialog {
}

.my_dialog_mask {
  z-index: 998;
  position: fixed;
  left: 0;
  top: 0;
  bottom: 0;
  right: 0;
  background-color: #000;
  opacity: 0.5;
}

.my_dialog_box {
  z-index: 999;
  position: absolute;
  height: 100%;
  max-width: 100%;
  max-height: 85%;
  border-radius: 3px;
  transform: translate(-50%, -50%);
}

.my_dialog_content {
  min-height: 100px;
  // overflow-x: hidden;
  height: 100%;
  overflow-y: auto;
  position: relative;
  /* padding: 20px; */
  text-align: left;
  box-sizing: border-box;
}

.my_dialog_title {
  cursor: all-scroll;
  word-break: keep-all;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  position: relative;
  top: 0;
  left: 0;
  height: 40px;
  line-height: 40px;
  border-bottom: 1px solid #e7e8eb;
  color: #000;
  font-size: 18px;
  font-family: \5fae\8f6f\96c5\9ed1;
  padding: 0 31px 0 18px;
  text-align: left;
  user-select: none;
}

.my_dialog_close {
  z-index: 99;
  cursor: pointer;
  position: absolute;
  top: 23px;
  margin-top: -8px;
  right: 20px;
  width: 16px;
  height: 16px;
  line-height: 16px;
  color: #ccc;
}
.my_dialog_close img {
  transform: rotateZ(0deg);
  transition: all 0.4s;
}
.my_dialog_close:hover img {
  color: #409eff;
  transform: rotateZ(-180deg);
  transition: all 0.4s;
}

.my_dialog_bottom {
  margin: 0;
  padding: 16px 0;
  text-align: center;
  border-top: 1px solid transparent;
}

.btn {
  min-width: 60px;
  text-align: center;
  vertical-align: middle;
  font-size: 14px;
  padding: 5px 15px;
  border-radius: 3px;
  text-decoration: none;
  border-radius: 3px;
  cursor: pointer;
}

.my_dialog_bottom .cancelBtn:focus,
.my_dialog_bottom .cancelBtn:hover {
  color: #409eff;
  background: #ecf5ff;
  border: 1px solid #b3d8ff;
}

.my_dialog_bottom .confirmBtn:focus,
.my_dialog_bottom .confirmBtn:hover {
  background: #66b1ff;
  border: 1px solid #66b1ff;
  color: #fff;
}

.my_dialog_bottom .confirm_btn .marginLeft {
  margin-left: 10px;
}

.cancelBtn {
  border: 1px solid #dcdfe6;
  background-color: #fff;
  color: #606266;
}

.confirmBtn {
  border: 1px solid #409eff;
  background-color: #409eff;
  color: #fff;
}

button + button {
  margin-left: 15px;
}
</style>
