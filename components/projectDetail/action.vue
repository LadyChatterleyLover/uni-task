<template>
  <view class="flex justify-end mt-15" v-if="detail">
    <view class="mr-15" @click="addUser"><u-avatar :text="detail.projectUsers[0].username.slice(0, 2)" size="32" font-size="14" bg-color="#0aa869"></u-avatar></view>
    <view class="item mr-15 flex justify-center items-center"><u-icon name="plus" color=" #606266"></u-icon></view>
    <view class="item mr-15 flex justify-center items-center"><u-icon name="search" color="#606266"></u-icon></view>
    <view class="item mr-15 flex justify-center items-center"><u-icon name="chat" color="#606266"></u-icon></view>
    <view class="item mr-15 flex justify-center items-center"><u-icon name="more-dot-fill" color="#606266"></u-icon></view>
  </view>
  <addProjectUser ref="addProjectUserRef" :detail="detail"></addProjectUser>
</template>

<script setup lang="ts">
import {
	ref,
} from 'vue'
import {
	ProjectItem
} from '../../types/project'
import addProjectUser from '../common/addProjectUser.vue'

const props = defineProps < {
	detail: ProjectItem
} > ()
const addProjectUserRef = ref()

const addUser = () => {
	addProjectUserRef.value?.open()
}
</script>

<style lang="scss" scoped>
.item {
  height: 64rpx;
  width: 64rpx;
  background: #f2f3f5;
  border-radius: 100%;
}
</style>
