<template>
    <div class='notifyList'>
        <el-empty v-if="props.list.length === 0" />
        <el-card v-else v-for="(item, index) in props.list" :key="index">

            <template #header>
                <div class="card-header">
                    <div>
                        <span class="card-title"> {{ item.title }}</span>
                        <el-tag v-if="item.extra" :type="item.status" effect="plain" size="small">{{ item.extra }}</el-tag>
                        <div class="card-datetime">{{ item.datetime }}</div>
                    </div>
                    <div v-if="item.avatar" class="card-avatar">
                        <img :src="item.avatar" width="34" />
                    </div>
                </div>
            </template>
            <div class="card-body">{{ item.description ?? 'NoData' }}</div>
        </el-card>
    </div>
</template>

<script setup lang='ts'>
import { PropType } from 'vue';
import { type ListItem } from './data'

const props = defineProps({
    list: {
        type: Array as PropType<ListItem[]>,
        required: true
    }
}) 
</script>

<style scoped lang='scss'>
.notifyList {
    .card-header {
        display: flex;
        align-items: center;
        justify-content: space-between;
    }

    .card-title {
        margin-right: 10px;
        font-weight: bold;
    }

    .card-datetime {
        font-size: 12px;
        color: grey;
    }

    // 头像处于垂直居中
    .card-avatar {
        display: flex;
        align-items: center;
    }

    .card-body {
        font-size: 12px;
    }
}</style>