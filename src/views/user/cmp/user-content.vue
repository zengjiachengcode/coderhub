<script setup>
import { useRouter } from 'vue-router'
import { ref, defineEmits } from 'vue'
const emits = defineEmits(['createMoment', 'removeMoment'])
const router = useRouter()
defineProps({
  moments: {
    type: Array,
    default: () => []
  }
})
const username = localStorage.getItem('username')
const token = localStorage.getItem('token')
const momentDetail = (id) => {
  router.push(`/detail/${id}`)
}

const momentContent = ref('')
const visible = ref(false)

const handleClick = () => {
  visible.value = true
}
const handleOk = () => {
  emits('createMoment', momentContent.value)
  visible.value = false
  momentContent.value = ''
}
const handleCancel = () => {
  visible.value = false
}

const deleteVisible = ref(false)
const curId = ref('')
const removeMoment = (id) => {
  console.log(id)
  deleteVisible.value = true
  curId.value = id
}
const deleteHandleOk = () => {
  emits('removeMoment', curId.value)
  deleteVisible.value = false
}
</script>

<template>
  <div class="user-content">
    <a-tabs position="left">
      <a-tab-pane key="1" title="动态">
        <div class="add-moment-section" v-if="$route.params.username === username && token">
          <!-- 发布动态对话框 -->
          <a-modal v-model:visible="visible" @ok="handleOk" @cancel="handleCancel" okText="发布">
            <template #title> 动态内容 </template>
            <a-textarea
              v-model="momentContent"
              placeholder="欢迎评论"
              :max-length="255"
              allow-clear
              show-word-limit
              :auto-size="{
                minRows: 4,
                maxRows: 8
              }"
            />
          </a-modal>
          <a-button type="primary" @click="handleClick">发布动态+</a-button>
        </div>
        <a-comment
          v-for="item in moments"
          :author="item.userInfo.nickname"
          :datetime="item.createAt"
          :key="item.id"
          class="content-item"
          align="right"
        >
          <template #actions>
            <span class="action" key="heart" @click="onLikeChange">
              <IconHeart />
              {{ item.likes }}
            </span>
            <span class="action" key="reply"> <IconMessage /> {{ item.commentCount }} </span>
            <span
              class="delete"
              @click="removeMoment(item.id)"
              v-if="$route.params.username === username && token"
            >
              <icon-delete /> 删除
            </span>
          </template>
          <template #content>
            <div @click="momentDetail(item.id)" class="moment-content">
              {{ item.content }}
            </div>
          </template>
        </a-comment>
        <!-- 删除对话框 -->
        <a-modal v-model:visible="deleteVisible" @ok="deleteHandleOk" :simple="true">
          <div>
            <icon-exclamation-circle-fill style="color: rgb(var(--warning-6))" />
            你确定要删除此动态吗？删除后不可恢复！
          </div>
        </a-modal>
      </a-tab-pane>
      <a-tab-pane key="2" title="文章"> Content of Tab Panel 2 </a-tab-pane>
      <a-tab-pane key="3" title="收藏"> Content of Tab Panel 3 </a-tab-pane>
      <a-tab-pane key="4" title="关注"> Content of Tab Panel 4 </a-tab-pane>
    </a-tabs>
  </div>
</template>

<style lang="less" scoped>
.user-content {
  background-color: var(--color-bg-1);
  margin: 20px 0;
  padding: 20px;
  .moment-content {
    cursor: pointer;
  }
}
.add-moment-section {
  text-align: right;
}
.delete {
  cursor: pointer;
  &:hover {
    color: rgb(var(--warning-6));
  }
}
</style>
