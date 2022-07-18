<template lang="pug">
#product
  v-row
    v-col(cols='12')
      h1.text-center {{ product.name }}
    v-divider
    v-col.text-center(cols='12')
      v-img(:src='product.image')
      p ${{ product.price }}
    v-col(cols='12')
      p(style='white-space: pre;') {{ product.description }}
    v-col(cols='12')
      v-form(v-model='valid' @submit.prevent='submit')
        v-text-field(type="number" label="數量" v-model="quantity" :rules='quantityRule')
        v-btn(color='primary' type='submit') 加入購物車
    v-overlay.align-center.justify-center(:model-value="!product.sell")
      h1.text-black 已下架
</template>

<script setup>
import { reactive, ref } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { api } from '@/plugins/axios'
import Swal from 'sweetalert2'
import { useUserStore } from '@/stores/user'

const route = useRoute()
const router = useRouter()
const user = useUserStore()

const quantity = ref(0)
const quantityRule = reactive([
  v => v >= 1 || '數量錯誤'
])
const valid = ref(false)

const product = reactive({
  _id: '',
  name: '',
  price: 0,
  category: '',
  sell: true,
  image: '',
  description: ''
})

const submit = () => {
  if (!valid.value) return
  user.addCart({ product: product._id, quantity: quantity.value })
}
const init = async () => {
  try {
    const { data } = await api.get('/products/' + route.params.id)
    product._id = data.result._id
    product.name = data.result.name
    product.price = data.result.price
    product.category = data.result.category
    product.sell = data.result.sell
    product.image = data.result.image
    product.description = data.result.description
  } catch (error) {
    Swal.fire({
      icon: 'error',
      title: '失敗',
      text: '取得商品資料失敗'
    })
    router.go(-1)
  }
}
init()
</script>
