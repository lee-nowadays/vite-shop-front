<template lang="pug">
v-row#register
  v-col(cols='12')
    h1.text-center 註冊
  v-divider
  v-col(cols='12')
    v-form(v-model='valid' @submit.prevent='register')
      v-text-field(
        type='email'
        label='信箱'
        v-model='form.email'
        :rules='rules.email'
      )
      v-text-field(
        type='text'
        label='帳號'
        v-model='form.account'
        :rules='rules.account'
        counter='20'
        maxlength="20"
      )
      v-text-field(
        type='password'
        label='密碼'
        v-model='form.password'
        :rules='rules.password'
        counter='20'
        maxlength="20"
      )
      v-btn.mx-auto(color='success' type='submit' :loading='loading') 註冊
</template>

<script setup>
import { reactive, ref } from 'vue'
import { isEmail } from 'validator'
import { api } from '@/plugins/axios'
import { useRouter } from 'vue-router'
import Swal from 'sweetalert2'

const router = useRouter()

const valid = ref(false)
const loading = ref(false)

const form = reactive({
  email: '',
  account: '',
  password: ''
})

const rules = reactive({
  email: [
    v => !!v || '信箱必填',
    v => isEmail(v) || '信箱格式錯誤'
  ],
  account: [
    v => !!v || '帳號必填',
    v => (v.length >= 4 && v.length <= 20) || '帳號長度為 4 到 20 個字',
    v => /^[a-zA-Z0-9]+$/.test(v) || '帳號只能由英數字組成'
  ],
  password: [
    v => !!v || '密碼必填',
    v => (v.length >= 4 && v.length <= 20) || '密碼長度為 4 到 20 個字',
    v => /^[a-zA-Z0-9]+$/.test(v) || '密碼只能由英數字組成'
  ]
})

const register = async () => {
  if (!valid.value) return
  loading.value = true
  try {
    await api.post('/users', form)
    await Swal.fire({
      icon: 'success',
      title: '成功',
      text: '註冊成功'
    })
    router.push('/')
  } catch (error) {
    Swal.fire({
      icon: 'error',
      title: '失敗',
      text: (error.isAxiosError && error.response.data) ? error.response.data.message : '發生錯誤'
    })
  }
  loading.value = false
}
</script>
