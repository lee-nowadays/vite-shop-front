<template lang="pug">
#home
  v-row
    v-col(v-if='products.length > 0' cols='12' md='6' lg='3' v-for='product in products' :key='product._id')
      ProductCard(:product='product')
    v-col(v-else cols='12')
      h1.text-center(v-if="loaded") 沒有商品
      h1.text-center(v-else indeterminate color="primary") 載入中
</template>

<script setup>
import { reactive, ref } from 'vue'
import Swal from 'sweetalert2'
import { api } from '@/plugins/axios'
import ProductCard from '@/components/ProductCard.vue'

const products = reactive([])
const loaded = ref(false)
const init = async () => {
  try {
    const { data } = await api.get('/products')
    products.push(...data.result)
  } catch (error) {
    Swal.fire({
      icon: 'error',
      title: '失敗',
      text: '伺服器錯誤'
    })
  }
  loaded.value = true
}
init()
</script>
