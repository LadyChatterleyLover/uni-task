<template>
	<view>
		<list></list>
	</view>
</template>

<script setup lang="ts">
	import {
		ref,
		onMounted
	} from 'vue'
	import {
		onLoad,
		onShow
	} from '@dcloudio/uni-app'

	import list from '../../components/project/list.vue'
</script>

<style lang="scss" scoped></style>
