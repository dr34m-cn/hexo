---
title: Vue3 + Element-Plus 极简速查
tags:
  - 经验
  - Vue
  - 编程
  - 前端
index_img: /assets/headImg/vue.png
date: 2026-01-12 13:36:24
---

## Vue3

### onMounted

```js
import { onMounted } from 'vue';
onMounted(() => {
    console.log('页面加载完成');
})
```

### defineProps

```js
const props = defineProps({
    title: {
        type: String,
        default: '创建策略'
    }
})

const getData = () => {
    let lm = props.title;
}
```

```html
<span>
    {{title}}
</span>
```

### watch

```js
import { watch } from "vue";
watch(() => props.title, (newTitle) => {
    console.log('newTitle', newTitle);
}, {
    deep: true, // 深度
    immediate: true // 首次也执行
});
const titleVal = ref('');
watch(titleVal, (newVal, oldVal) => {
    console.log('titleVal', newVal);
});
```

### defineExpose

```js
// rst.vue
const getData = () => {
    console.log('getData');
}
defineExpose({
    getData
})
```

```js
const rstRef = ref();
const getIt = () => {
    rstRef.value.getData();
}
```

```html
<rst ref="rstRef"></rst>
```

### defineEmits

```js
// rst.vue
const emit = defineEmits(['closeDialog'])
const close = () => {
    emit('closeDialog', true);
}
```

```js
const doSth = (flag) => {
    console.log('flag', flag);
}
import rst from './components/rst.vue';
```

```html
<rst @closeDialog="doSth"></rst>
```

## Element-Plus

### ElMessageBox

```js
import { ElMessage, ElMessageBox } from 'element-plus';
const delItem = (id) => {
    ElMessageBox.confirm('删除后不可恢复，确认删除吗？', '危险', {
        cancelButtonText: '取消',
        confirmButtonText: '确定',
        cancelButtonType: 'success',
        confirmButtonType: 'danger'
    }).then(() => {
        loading.value = true;
        deleteIt(id).then(res => {
            ElMessage({
                type: 'success',
                message: res.msg,
            })
            getData();
        }).finally(() => {
            loading.value = false;
        })
    }).catch(() => { })
}
```

### [el-table](https://element-plus.org/zh-CN/component/table#table-api)

```js
const getData = () => {
    // do
}
const params = ref({
    pageNum: 1,
    pageSize: 15
})
const tableData = ref({
    dataList: [],
    count: 0
})
const handleSizeChange = (val) => {
    params.value.pageSize = val;
    getData();
}
const handleCurrentChange = (val) => {
    params.value.pageNum = val;
    getData();
}
```

```html
<div class="center-main">
    <el-table v-loading="loading" current-row-key="id" :data="tableData.dataList" stripe
        style="width: 100%;height: 100%">
        <el-table-column type="index" label="序号" width="60" />
        <el-table-column prop="name" label="策略名称" />
        <el-table-column label="操作" width="200">
            <template #default="scope">
                <el-button type="danger" size="small" @click="delItem(scope.row.id)">删除</el-button>
                <el-button type="primary" size="small" @click="showConfig(scope.row.id)">配置</el-button>
            </template>
        </el-table-column>
    </el-table>
    <div class="page-box">
        <el-pagination layout="total, sizes, prev, pager, next, jumper" :background="true"
             v-model:page-size="params.pageSize" :page-sizes="[15, 50, 300, 500]"
             v-model:current-page="params.pageNum" :total="tableData.count"
             @size-change="handleSizeChange" @current-change="handleCurrentChange" />
    </div>
</div>
```

### [el-dialog](https://element-plus.org/zh-CN/component/dialog#api)

```js
const dialogShow = ref(false);
const showDialog = () => {
    dialogShow.value = true;
}
const closeDialog = () => {
    dialogShow.value = false;
}
```

```html
<el-dialog top="7vh" :fullscreen="false" :before-close="closeDialog"
           v-model="dialogShow" width="1200px" title="Dialog"
           :append-to-body="true" :close-on-click-modal="false">
    <template #footer>
        <span class="dialog-footer">
            <el-button type="primary" @click="closeDialog">我已知晓</el-button>
        </span>
    </template>
</el-dialog>
```

