<template>
	<u-popup :show="show">
		<u-list>
			<u-list-item v-for="(item, index) in userList" :key="index">
				<u-cell :title="item.username">
					<template #icon>
						<u-checkbox-group>
							<u-checkbox v-model="item.checked" shape="circle"></u-checkbox>
						</u-checkbox-group>
						<u-avatar size="35" :text="item.avatar" class="mx-10">
						</u-avatar>
					</template>
				</u-cell>
			</u-list-item>
		</u-list>
	</u-popup>
</template>

<script setup lang="ts">
	import {
		ref,
		onMounted
	} from 'vue'
	const db = uniCloud.database()
	const userCollection = db.collection('uni-id-users')

	const show = ref(false)
	const userList = ref([])

	const getUser = () => {
		userCollection.field('_id,username,avatar').get().then(res => {
			userList.value = res.result.data
			userList.value.map(item => {
				item.checked = false
				item.avatar = item.username.slice(-2)
			})
			console.log(userList.value)
		})
	}

	const open = () => {
		show.value = true
	}

	onMounted(() => {
		getUser()
	})
	defineExpose({
		open
	})
</script>

<style lang="scss" scoped></style>
