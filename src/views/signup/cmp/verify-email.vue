<script setup>
import { ref } from 'vue'
import { fetchVerifyEmail } from '@/service'
const emailVal = ref('')
const isVerify = ref(false)
const tipRef = ref('')
import _debounce from '@/utils/debounce'

async function verifyEmailInput() {
  if (
    !/^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(
      emailVal.value
    )
  ) {
    tipRef.value.textContent = '邮箱格式错误'
    tipRef.value.style.color = ' rgb(var(--danger-6))'
    isVerify.value = false
    return
  }
  const res = await fetchVerifyEmail(emailVal.value)
  if (res.code !== 200) {
    tipRef.value.textContent = '邮箱已经被注册'
    tipRef.value.style.color = ' rgb(var(--danger-6))'
    isVerify.value = false
    return
  }
  tipRef.value.textContent = '邮箱符合要求'
  tipRef.value.style.color = 'rgb(var(--success-6))'
  isVerify.value = true
}
// 对邮箱验证进行防抖处理
const verifyEmailInput_debounce = _debounce(verifyEmailInput, 500)

defineExpose({
  verifyEmailInput,
  emailVal,
  isVerify
})
</script>

<template>
  <div class="email">
    <a-input
      @input="verifyEmailInput_debounce"
      v-model="emailVal"
      :style="{ width: '320px' }"
      placeholder="请输入邮箱"
      allow-clear
    >
      <template #prefix>
        <icon-email />
      </template>
    </a-input>
    <div class="tip" ref="tipRef">请输入正确的邮箱</div>
  </div>
</template>

<style scoped>
.tip {
  font-size: 10px;
  text-align: center;
  margin-top: 3px;
}
</style>
