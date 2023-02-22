<template>
	<view style="background: #f8f8f8" class="h-screen">
		<view class="flex justify-end mr-10 mb-10">
			<view class="px-8 py-6 text-white text-sm rounded-xs" style="background: #409eff;" @click="add">创建项目
			</view>
		</view>
		<u-search placeholder="搜索项目" :show-action="false" bgColor="#fff" v-model="value" @search="confirm" />
		<view v-if="!projectList.length" class="mr-5 mt-5">
			<view class="flex justify-center text-lg mt-10">暂无项目</view>
		</view>
		<view v-else>
			<view class="mt-15">
				<view v-for="item in projectList" :key="item._id" class="rounded-lg m-16 p-16 bg-white text-sm"
					@click="goDetail(item)">
					{{item.name}}
				</view>
			</view>
		</view>
	</view>
</template>

<script setup lang="ts">
	import {
		ref,
		onMounted
	} from 'vue'

	import {
		ProjectItem
	} from '../../types/project'

	const db = uniCloud.database()
	const projectCollection = db.collection('project')
	const projectList = ref < ProjectItem[] > ([])
	const value = ref('')

	const getData = () => {
		projectCollection.get().then(res => {
			projectList.value = res.result.data
		})
	}

	const add = () => {
		uni.showModal({
			title: '添加项目',
			editable: true,
			placeholderText: "请输入项目名称",
			confirmColor: '#a2d98d',
			success: async (res) => {
				uni.showLoading({
					title: '加载中...'
				})
				const content = res.content
				if (!content) {
					uni.showToast({
						title: '项目名称不能为空',
						icon: 'error'
					})
					return
				}
				const project = await projectCollection.where({
					name: content
				}).get()
				const data = project.result.data
				if (data.length) {
					uni.showToast({
						title: '项目已存在',
						icon: 'error'
					})
					return
				}
				const userResult = await db.collection('uni-id-users').where('_id==$cloudEnv_uid').field(
					'_id,username').get()
				const projectUsers = []
				projectUsers.push(userResult.result.data[0])
				const result = await projectCollection.add({
					name: content,
					projectUsers
				})
				if (result.errMsg) {
					uni.showToast({
						title: result.errMsg,
						icon: 'error'
					})
				} else {
					uni.showToast({
						title: '创建成功',
						icon: 'success'
					})
					getData()
				}
			},
			complete: () => {
				uni.hideLoading()
			}
		})
	}

	const confirm = () => {
		projectCollection.where({
			name: new RegExp(value.value, 'i')
		}).get().then(res => {
			projectList.value = res.result.data
		})
	}

	const goDetail = (item: ProjectItem) => {
		uni.navigateTo({
			url: '/pages/projectDetail/projectDetail?item=' + JSON.stringify(item)
		})
	}

	onMounted(() => {
		getData()
	})
</script>

<style lang="scss" scoped></style>
