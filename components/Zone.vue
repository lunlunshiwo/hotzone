<template>
  <li
    v-drag-item
    class="zoneBox"
    :style="{top: zoneTop, left: zoneLeft, width: zoneWidth, height: zoneHeight}"
  >
    <ul
      v-change-size
      class="hz-m-box"
      :class="{
        'hz-z-hidden': tooSmall,
        'hz-m-hoverbox': !hideZone
      }"
      :dataUrl="setting.url"
      :style="{
        backgroundColor:setting.url?'#373950':'#e31414'
      }"
      @dblclick="setZoneMessage"
    >
      <li
        class="hz-u-index"
        :title="`热区${index + 1}-${setting.url}`"
      >{{ index + 1 }}</li>
      <li
        title="删除该热区"
        v-show="!hideZone"
        class="hz-u-close hz-icon hz-icon-trash title"
        @click.stop="delItem(index)"
        @mousedown.stop
      ></li>
      <li
        class="hz-u-square hz-u-square-tl"
        data-pointer="dealTL"
      ></li>
      <li
        class="hz-u-square hz-u-square-tc"
        data-pointer="dealTC"
      ></li>
      <li
        class="hz-u-square hz-u-square-tr"
        data-pointer="dealTR"
      ></li>
      <li
        class="hz-u-square hz-u-square-cl"
        data-pointer="dealCL"
      ></li>
      <li
        class="hz-u-square hz-u-square-cr"
        data-pointer="dealCR"
      ></li>
      <li
        class="hz-u-square hz-u-square-bl"
        data-pointer="dealBL"
      ></li>
      <li
        class="hz-u-square hz-u-square-bc"
        data-pointer="dealBC"
      ></li>
      <li
        class="hz-u-square hz-u-square-br"
        data-pointer="dealBR"
      ></li>
      <div
        class="title-box"
        v-if="setting.url"
        :style="{
          top:setting.topPer>0.5?'initial':'100%',
          bottom:setting.topPer>0.5?'100%':'initial',
          left:setting.leftPer>0.5?'initial':'100%',
          right:setting.leftPer>0.5?'100%':'initial'
        }"
      >
        <p>跳转链接：<a
            :title="setting.url"
            target="_blank"
            :href="setting.url"
          >{{setting.url}}</a></p>
      </div>
      <li
        class="hz-m-btns"
        v-if="setting.url"
      >
        <span
          class="iconBtn"
          title="设置网址"
          @click="setZoneMessage"
        >
          <i class="el-icon-edit"></i>
        </span>
        <span
          class="iconBtn"
          title="复制网址"
          @click="copyUrl"
        >
          <i class="el-icon-share"></i>
        </span>
      </li>
    </ul>
  </li>
</template>

<script>
import changeSize from '../directives/changeSize'
import dragItem from '../directives/dragItem'
import { Message } from 'element-ui'

export default {
  name: 'Zone',
  data() {
    return {
      zoneTop: '',
      zoneLeft: '',
      zoneWidth: '',
      zoneHeight: '',
      hideZone: false,
      tooSmall: false
    }
  },
  props: [
    'index',
    'setting'
  ],
  mounted() {
    this.setZoneInfo(this.setting)
  },
  methods: {
    setZoneInfo(val) {
      this.zoneTop = this.getZoneStyle(val.topPer)
      this.zoneLeft = this.getZoneStyle(val.leftPer)
      this.zoneWidth = this.getZoneStyle(val.widthPer)
      this.zoneHeight = this.getZoneStyle(val.heightPer)
      this.tooSmall = val.widthPer < 0.01 && val.heightPer < 0.01
    },
    handlehideZone(isHide = true) {
      if (this.hideZone === isHide) {
        return
      }
      this.hideZone = isHide
    },
    changeInfo(info = {}) {
      const { index } = this
      this.$emit('changeInfo', {
        info,
        index
      })
    },
    delItem(index) {
      this.$emit('delItem', index)
    },
    getZoneStyle(val) {
      return `${(val || 0) * 100}%`
    },
    setZoneMessage() {
      this.$emit('setZoneMessage', this.index)
    },
    copyUrl() {
      const oInput = document.createElement('input')
      oInput.value = this.setting.url
      document.body.appendChild(oInput)
      oInput.select() // 选择对象
      document.execCommand('Copy') // 执行浏览器复制命令
      oInput.className = 'oInput'
      oInput.style.display = 'none'
      Message.success('复制成功')
    }
  },
  watch: {
    setting: {
      handler(val) {
        this.setZoneInfo(val)
      },
      deep: true
    }
  },
  directives: {
    changeSize,
    dragItem
  }
}
</script>
<style scoped>
.zoneBox:hover .title-box {
  display: block;
}
.title-box {
  display: none;
  position: absolute;
  padding: 5px 10px;
  line-height: 20px;
  opacity: 0.8;
  color: #fff;
  z-index: 10;
  background-color: #000;
  cursor: default;
  user-select: none;
  white-space: nowrap;
  top: 100%;
  left: 100%;
  bottom: initial;
  right: initial;
  transform: translate(0%, 0%);
}
.title-box > p > a {
  cursor: pointer;
  color: #fff;
  word-break: break-word;
  white-space: normal;
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  line-clamp: 3;
  box-orient: vertical;
  text-decoration: underline;
}
.hz-m-btns {
  min-width: 58px;
  bottom: 5px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  justify-content: space-around;
}
.iconBtn {
  width: 24px;
  height: 24px;
  line-height: 24px;
  font-size: 20px;
  text-align: center;
  border: 1px solid;
  border-radius: 4px;
}
</style>
