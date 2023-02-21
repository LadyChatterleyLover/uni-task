<template>
	<view class="p-15 flex flex-col" style="margin-top: 160rpx;">
		<view class="text-2xl text-bold ">
			欢迎您，{{user.username}}
		</view>
		<view class="text-sm mt-10 mb-10" style="color: #888;">
			以下是你当前的任务统计数据
		</view>
		<view v-for="(item, index) in list" :key="index" class="mb-20 rounded-sm px-10 py-16 text-white"
			:style="{background: item.bg}">
			<view class="text-sm">{{item.title}}</view>
			<view class="flex items-center justif-between mt-12">
				<view>{{item.count}}</view>
				<view>
					<uni-icons :type="item.icon" color="#fff"></uni-icons>
				</view>
			</view>
		</view>
	</view>
</template>

<script lang="ts" setup>
	interface ListItem {
		title: string
		icon: string
		bg: string
		count: number
	}

	const user = uni.getStorageSync('uni-id-pages-userInfo')
	const list: ListItem[] = [{
			title: '今日到期',
			icon: 'chat',
			bg: '#6f9ef6',
			count: 0
		},
		{
			title: '超期任务',
			icon: 'notification',
			bg: '#fa8e8c',
			count: 0
		},
		{
			title: '待完成任务',
			icon: 'compose',
			bg: '#98de6e',
			count: 0
		}
	]
</script>

<style lang="scss" scoped>

</style>
