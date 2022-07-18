<template lang="pug">
#home
  v-row
    v-col(v-if='products.length > 0' cols='12' md='6' lg='3' v-for='product in products' :key='product._id')
      ProductCard(:product='product')
    v-col(v-else cols='12')
      h1.text-center 沒有商品
</template>

<script setup>
import { reactive } from 'vue'
import Swal from 'sweetalert2'
import { api } from '@/plugins/axios'
import ProductCard from '@/components/ProductCard.vue'

const products = reactive([])

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
}
init()
</script>
