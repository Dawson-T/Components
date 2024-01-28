<template>
    <div class='notify'>
        <!-- 弹出框 -->
        <el-popover trigger="click" :width="popoverWidth">
            <!-- 触发元素 -->
            <template #reference>
                <el-badge :value="badgeValue" :max="badgeMax" :hidden="badgeValue === 0">
                    <el-tooltip effect='dark' content='消息通知' placement='bottom'>
                        <el-icon :size="20">
                            <Bell />
                        </el-icon>
                    </el-tooltip>
                </el-badge>
            </template>

            <!-- 触发内容 -->
            <template #default>
                <el-tabs v-model="activeName" stretch>
                    <el-tab-pane v-for="(item, index) in data" :name="item.name" :key="index">
                        <template #label>
                            {{ item.name }}
                            <el-badge :value="item.list.length" :max="badgeMax" :type='item.type'></el-badge>
                        </template>
                        <el-scrollbar height="400px">
                            <NotifyList :list="item.list" />
                        </el-scrollbar>
                    </el-tab-pane>

                </el-tabs>
                <div class="notify-history">
                    <el-button link @click="handleHistory()">查看{{ activeName }}历史</el-button>
                </div>
            </template>
        </el-popover>
    </div>
</template>

<script setup lang='ts'>
import { Bell } from "@element-plus/icons-vue"
import { ElMessage } from "element-plus";
import { computed, ref } from "vue";
import { type ListItem, notifyData, messageData, todoData } from "./data"
import NotifyList from "./NotifyList.vue";

const popoverWidth = 350

const badgeMax = 99

const badgeValue = computed(() => {
    return data.value.reduce((acc, cur) => acc + cur.list.length, 0)
})

const activeName = ref('通知')

const handleHistory = () => {
    ElMessage.success(`跳转到${activeName.value}历史页面`)
}

type TabName = "通知" | "消息" | "待办"

interface DataItem {
    name: TabName
    type: "primary" | "danger" | "warning"
    list: ListItem[]
}

/** 所有数据 */
const data = ref<DataItem[]>([
    // 通知数据
    {
        name: "通知",
        type: "primary",
        list: notifyData
    },
    // 消息数据
    {
        name: "消息",
        type: "danger",
        list: messageData
    },
    // 待办数据
    {
        name: "待办",
        type: "warning",
        list: todoData
    }
])
</script>

<style scoped lang='scss'>
.notify {
    margin-right: 10px;
}

.notify-history {
    text-align: center;
    margin-top: 10px;
    border-top: 1px solid #eee;
}
</style>