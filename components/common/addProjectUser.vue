<template>
  <u-popup :show="show">
    <view class="list">
      <u-list>
        <u-list-item v-for="(item, index) in userList" :key="index">
          <u-cell :title="item.username">
            <template #icon>
              <view @click="clickItem(item)">
                <view class="h-20 w-20 checkbox flex justify-center items-center" :class="{ disabled: item.username === currentUser.username }" v-if="!item.checked">
                  <u-icon v-if="item.username === currentUser.username" name="checkbox-mark" color="#fff"></u-icon>
                </view>
                <view v-else class="h-20 w-20 checkbox flex justify-center items-center" style="background: #2979ff;"><u-icon name="checkbox-mark" color="#fff"></u-icon></view>
              </view>
              <u-avatar size="35" :text="item.avatar" class="mx-10"></u-avatar>
            </template>
          </u-cell>
        </u-list-item>
      </u-list>
    </view>
    <view class="fixed b-0 l-0 r-0 h-50 flex justif-between items-center px-10 action">
      <view>已选择: {{ checkedList.length }}</view>
      <view class="flex items-center">
        <u-button class="mr-10" @click="cancel">取消</u-button>
        <u-button type="primary" @click="confirm">确认</u-button>
      </view>
    </view>
  </u-popup>
</template>

<script setup lang="ts">
import { ref, onMounted, computed, watch } from 'vue'
import { ProjectItem } from '../../types/project'

const props = defineProps<{
  detail: ProjectItem
}>()

const db = uniCloud.database()
const userCollection = db.collection('uni-id-users')
const projectCollection = db.collection('project')
const currentUser = uni.getStorageSync('uni-id-pages-userInfo')

const show = ref(false)
const userList = ref([])

const checkedList = computed(() => userList.value.filter(item => item.checked))

const getUser = () => {
  userCollection
    .field('_id,username,avatar')
    .get()
    .then(async res => {
      const projectResult = await projectCollection.doc(props.detail._id).get()
      const arr = projectResult.result.data[0].projectUsers.map(item => item.username)
      userList.value = res.result.data
      userList.value.map(item => {
        if (arr.includes(item.username)) {
          item.checked = true
        } else {
          item.checked = false
        }
        item.avatar = item.username.slice(-2)
      })
    })
}

const clickItem = item => {
  if (item.username === currentUser.username) {
    return
  }
  item.checked = !item.checked
}

const confirm = async () => {
  const arr = [
    {
      _id: currentUser._id,
      username: currentUser.username
    }
  ]
  checkedList.value.map(item => {
    arr.push({
      _id: item._id,
      username: item.username
    })
  })
  projectCollection
    .doc(props.detail._id)
    .update({
      projectUsers: arr
    })
    .then(res => {
      if (res.result.code === 0) {
        uni.showToast({
          title: '更新成功',
          icon: 'success'
        })
      } else {
        uni.showToast({
          title: '更新失败',
          icon: 'error'
        })
      }
    })
    .catch(() => {
      uni.showToast({
        title: '更新失败',
        icon: 'error'
      })
    })
    .finally(() => {
      show.value = false
    })
}

const cancel = () => {
  show.value = false
}

const open = () => {
  show.value = true
}

watch(
  () => show.value,
  val => {
    if (val) {
      getUser()
    }
  }
)

onMounted(() => {
  getUser()
})
defineExpose({
  open
})
</script>

<style lang="scss" scoped>
.list {
  height: calc(100% - 100rpx);
}
.checkbox {
  border: 1px solid #eee;
  border-radius: 100%;
}
.action {
  z-index: 100;
  border-top: 1px solid #eee;
  background: #fff;
}
.disabled {
  background: #ebedf0;
  border-color: #c8c9cc;
}
</style>
