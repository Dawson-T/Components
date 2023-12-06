<template>
  <el-button @click="open">打开</el-button>
  <!-- 弹窗 -->
  <div v-if="show" class="dialog layout_c layout_v layout_r">
    <div class="captcha layout_v" ref="captchaRef">
      <div class="subhead">人机验证</div>
      <div class="tips">{{ tips }}</div>
      <div class="image">
        <div
          class="imageContainer layout_r"
          :class="{ checking: status == 'Verifying' }"
        >
          <div
            class="src"
            :style="{
              backgroundImage: `url(${imgUrl})`,
              transform: `rotate(${angle}deg)`,
              transitionProperty: `${isDragging ? '' : 'all'}`,
            }"
          />
          <div
            v-if="status == 'Verified'"
            class="src"
            :style="{
              backgroundImage: `url(${sucImg})`,
            }"
          />
        </div>

        <!-- 验证成功 -->
        <div class="icon layout_c" v-if="false">
          <i class="success">验证成功</i>
        </div>
      </div>

      <div class="slider layout_r" ref="sliderRef">
        <div
          class="sliderItemContainer layout_c"
          :style="{
            marginLeft: `${currentWidth}px`,
            transitionProperty: `${
              isDragging ? '' : 'margin,box-show,background-color'
            }`,
          }"
          @touchstart="dragStart"
          @touchmove.prevent="dragMove"
          @touchend="dragEnd"
          @mousedown="dragStart"
          @mousemove.prevent="dragMove"
          @mouseup="dragEnd"
          @dragstart="dragStart"
          @dragend="dragEnd"
          @dragover.stop
          @drop="dragMove"
          @mouseleave="mouseLeave"
        >
          <div class="sliderItem layout_c">
            <div class="i-drag layout_c iconfont icon-RectangleCopy58" />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { onMounted, onUnmounted, ref } from 'vue'

const props = defineProps({
  initialShow: {
    type: Boolean,
    default: false,
  },
})

const messages = {
  default: '滑动滑块，使图片角度为正',
  verifying: '请稍后，验证中...',
  error: '请重试...',
  success: '验证成功！',
}

const sliderWidth = 220 // 滚动条长度

let show = ref(props.initialShow)

const sliderRef = ref<HTMLElement | null>(null)
onMounted(() => {
  getImg()
  sliderRef.value?.addEventListener('mousemove', dragMove)
  sliderRef.value?.addEventListener('mouseup', dragEnd)
})

onUnmounted(() => {
  sliderRef.value?.removeEventListener('mousemove', dragMove)
  sliderRef.value?.removeEventListener('mouseup', dragEnd)
})

let isDragging = false // 是否拖动
let startX = 0 // 开始拖动位置
let currentWidth = ref(0) // 距离左侧宽度

let dragStart = function (e: MouseEvent | TouchEvent) {
  isDragging = true
  // 移动端
  if (e.type == 'touchstart') {
    startX = (e as TouchEvent).touches[0].clientX
  } else {
    startX = (e as MouseEvent).clientX
  }
}

let angle = ref(0)
let dragMove = function (e: MouseEvent | TouchEvent) {
  if (isDragging) {
    let deltaX = 0
    // 移动端
    if (e.type == 'touchmove') {
      deltaX = (e as TouchEvent).touches[0].clientX - startX
    } else {
      deltaX = (e as MouseEvent).clientX - startX
    }
    let newLeft = deltaX

    // 限制滑块在合法范围内移动
    if (newLeft < 0) {
      newLeft = 0
    } else if (newLeft > sliderWidth) {
      newLeft = sliderWidth
    }
    currentWidth.value = newLeft

    angle.value = (currentWidth.value / sliderWidth) * 360
  }
}
// 鼠标越出元素
let mouseLeave = function () {


  dragEnd()
}

let tips = ref(messages.default)
const status = ref('Verify') // 验证状态
let dragEnd = async function () {
  // 解决点击验证的问题
  if (!isDragging || currentWidth.value == 0) {
    isDragging = false
    return
  }
  isDragging = false
  // 验证中
  tips.value = messages.verifying
  status.value = 'Verifying'
  let result: any = await isRotateRight()
  if (result.code === 0) {
    sucImg.value =
      'https://nsrh.ctbu.edu.cn/uump/ImageVerification/Rotation?type=svg&time=1699519267635'
    status.value = 'Verified'
    tips.value = messages.success
    setTimeout(() => {
      close()
    }, 2000)
    return
  }
  status.value = 'Verify'
  tips.value = messages.error
  // 初始化
  angle.value = 0
  tips.value = messages.default
  currentWidth.value = 0
}

let open = function () {
  show.value = true
}

let close = function () {
  show.value = false

  // 重置弹窗
  status.value = 'Verify'
  tips.value = messages.default
  currentWidth.value = 0
  sucImg.value = ''
}

// =====================网络请求==========================
// 图片地址

let sucImg = ref('')

// 获取图片
let imgUrl = ref('')
let getImg = async function () {
  imgUrl.value = 'https://picsum.photos/200'
}
// 函数判断是否旋转正确
let isRotateRight = function () {
  return new Promise((solve) => {
    setTimeout(() => {
      solve({ code: 1 })
    }, 500)
  })
}
</script>

<style scoped lang="less">
.dialog {
  height: 100%;
  width: 100%;
  background: rgba(0, 0, 0, 0.4);
  z-index: 99999;
  position: absolute;
  top: 0;
  left: 0;
  .captcha {
    align-items: center;
    width: 300px;
    height: 400px;
    border-radius: 20px;
    background-color: #fff;
    .subhead {
      color: #8799a3;
      font-size: 14px;
      margin-top: 20px;
    }
    .tips {
      font-size: 17px;
      color: #333333;
      margin-top: 20px;
    }
    .image {
      height: 150px;
      width: 150px;
      margin-top: 30px;
      .imageContainer {
        height: 150px;
        width: 150px;
        border-radius: 50%;
        background-color: #eee;
        .src {
          background-size: cover;
          height: 150px;
          width: 150px;
          border-radius: 50%;
          transition-property: none;
          transition-duration: 0.5s;
        }
      }

      .icon {
        height: 100%;
        width: 100%;
      }
    }
    .slider {
      margin-top: 30px;
      width: 250px;
      height: 40px;
      border-radius: 40px;
      background-color: #eee;

      .sliderItemContainer {
        width: 100px;
        height: 100px;
        left: -30px;
        top: -30px;
        transition-property: box-shadow, background-color;
        transition-duration: 0.5s;
        .sliderItem {
          width: 40px;
          height: 40px;
          background-color: #fff;
          border-radius: 40px;
          line-height: 40px;
          color: #000;
          font-size: 15px;
          font-weight: bold;
          box-shadow: 1px 1px 10px 5px #888888;
          user-select: none;
          transition-property: box-shadow, background-color;
          transition-duration: 0.5s;
          .i-drag {
            font-size: 25px;
          }
        }
        .sliderItem:hover {
          box-shadow: 1px 1px 50px 5px #2196f3;
          background-color: #2196f3;
          color: #fff;
        }
      }
    }
  }
}

.checking {
  animation: checking 1.5s infinite;
}
@keyframes checking {
  0% {
    opacity: 0.5;
  }
  50% {
    opacity: 1;
  }
  100% {
    opacity: 0.5;
  }
}
</style>
