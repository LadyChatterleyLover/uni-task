<template>
	<view>
		<action :detail="detail"></action>
	</view>
</template>

<script setup lang="ts">
	import {
		ref
	} from 'vue'
	import {
		onLoad,
	} from '@dcloudio/uni-app'
	import {
		ProjectItem
	} from '../../types/project'
	import action from '../../components/projectDetail/action.vue'

	const detail = ref < ProjectItem > ()

	onLoad((e) => {
		detail.value = JSON.parse(e.item) as ProjectItem
		uni.setNavigationBarTitle({
			title: detail.value.name
		})
		console.log(detail.value)
	})
</script>

<style lang="scss" scoped></style>
