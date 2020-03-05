# hotzone
基于vue的热图插件，可以新增，设置等等


传入参数
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


触发事件
add 新增事件
remove 删除事件
change 改变事件

调用方式

import 引入

<hotzone
:image="image"
:zonesInit="zones"
:zoneSetState="false"
:selectList="selectList"
@add="handleAdd"
@remove="handleRemove"
@change="handleChange"
/>
