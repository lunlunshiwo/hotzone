<template>
  <div
    ref="content"
    class="hz-m-wrap hot-zone"
  >
    <img
      class="hz-u-img"
      :src="image"
    />
    <ul
      class="hz-m-area"
      v-add-item
    >
      <zone
        class="hz-m-item"
        v-for="(zone, index) in zones"
        :key="index"
        :index="index"
        :setting="zone"
        @delItem="removeItem($event)"
        @changeInfo="changeInfo($event)"
        @setZoneMessage="setZoneMessage"
      ></zone>
    </ul>
    <el-dialog
      title="热区设置"
      :visible.sync="dialogVisible"
      width="500px"
      center
    >
      <el-form
        ref="zoneForm"
        :model="form"
        :rules="rules"
        label-width="90px"
      >
        <el-form-item
          label="跳转链接:"
          :required="true"
          prop="url"
          v-if="zoneSetState"
        >
          <el-input
            v-model="form.url"
            placeholder="请输入跳转链接"
          />
        </el-form-item>
        <el-form-item
          label="跳转链接:"
          v-else
        >
          <el-select
            v-model="form.url"
            style="width: 100%"
          >
            <el-option
              v-for="item in selectList"
              :key="item.val"
              :label="item.title"
              :value="item.val"
            />
          </el-select>
        </el-form-item>
      </el-form>
      <span
        slot="footer"
        class="dialog-footer"
      >
        <el-button
          size="small"
          @click="dialogVisible = false"
        >取 消</el-button>
        <el-button
          type="primary"
          size="small"
          @click="setZoneUrl"
        >确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import Zone from './Zone'
import addItem from '../directives/addItem'
import _ from '../utils'

export default {
  name: 'HotZone',
  data() {
    return {
      zones: [],
      dialogVisible: false,
      zoneIndex: 0,
      form: {
        url: ''
      },
      rules: {
        url: [{
          required: true,
          trigger: ['blur', 'change'],
          validator: (rule, val, cb) => {
            if (!_.isURL(val)) {
              cb(new Error('请输入正确的跳转链接'))
            } else {
              cb()
            }
          }
        }]
      }
    }
  },
  props: {
    // 图片地址
    image: {
      type: String,
      required: true
    },
    // 预设热区
    zonesInit: {
      type: Array,
      default: () => []
    },
    // 最大热区数量
    max: {
      type: Number
    },
    // 下拉框选项
    selectList: {
      type: Array,
      default: () => []
    },
    // 网址输入形式，true为input
    zoneSetState: {
      type: Boolean,
      default: true
    }
  },
  mounted() {
    this.zones = this.zonesInit.concat()
  },
  methods: {
    changeInfo(res) {
      let { info, index } = res
      this.changeItem(info, index)
    },
    addItem(setting) {
      this.zones.push(setting)
      this.hasChange()
      this.$emit('add', setting)
    },
    eraseItem(index = this.zones.length - 1) {
      this.removeItem(index)
      this.$emit('erase', index)
    },
    isOverRange() {
      let { max, zones } = this
      return max && zones.length > max
    },
    overRange() {
      const index = this.zones.length - 1
      this.removeItem(index)
      this.$emit('overRange', index)
    },
    removeItem(index = this.zones.length - 1) {
      this.zones.splice(index, 1)
      this.hasChange()
      this.$emit('remove', index)
    },
    changeItem(info, index = this.zones.length - 1) {
      Object.assign(this.zones[index], info)
      this.hasChange()
    },
    hasChange() {
      this.$emit('change', this.zones)
    },
    setZoneMessage(index) {
      this.zoneIndex = index
      this.$set(this.form, 'url', this.zones[this.zoneIndex].url)
      this.dialogVisible = true
      this.clearForm()
    },

    addZoneMessage() {
      this.zoneIndex = this.zones.length - 1
      this.$set(this.form, 'url', this.zones[this.zoneIndex].url)
      this.dialogVisible = true
      this.clearForm()
    },
    clearForm() {
      this.$nextTick(() => {
        this.$refs.zoneForm.clearValidate()
      })
    },
    setZoneUrl() {
      this.$refs.zoneForm.validate((valid) => {
        if (valid) {
          const zone = { ...this.zones[this.zoneIndex], url: this.form.url }
          this.zones.splice(this.zoneIndex, 1, zone)
          this.hasChange()
          this.dialogVisible = false
        }
      })
    }
  },
  watch: {
    zones(val) {
      console.log(val)
    }
  },
  directives: {
    addItem
  },
  components: {
    Zone
  }
}
</script>

<style>
@import "../assets/styles/main.css";
.hot-zone .el-dialog__body {
  padding: 10px !important;
}
.hot-zone .el-dialog__footer {
  padding: 10px !important;
}
.hot-zone .el-form-item {
  margin-bottom: 5px !important;
}
</style>
