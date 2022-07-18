<template lang="pug">
#cart
  v-row
    v-col(cols='12')
      h1.text-center 購物車
    v-divier
    v-col(cols='12')
      v-table
        thead
          tr
            th 圖片
            th 名稱
            th 單價
            th 數量
            th 小計
            th 操作
        tbody
          tr(v-if='cart.length > 0' v-for='(item, idx) in cart' :key='item._id' :class="{'bg-red': !item.product.sell}")
            td
              v-img(:src='item.product.image')
            td {{ item.product.name }}
            td {{ item.product.price }}
            td
              v-btn(color='primary' @click='updateCart(idx, item.quantity-1)' variant="text") -
              | {{ item.quantity }}
              v-btn(color='primary' @click='updateCart(idx, item.quantity+1)' variant="text") +
            td {{ item.product.price * item.quantity }}
            td
              v-btn(color='primary' @click='updateCart(idx, 0)') 刪除
          tr(v-else)
            td(colspan="6")
              h3.text-center 沒有商品
    v-divider
    v-col.text-center(cols='12')
      p 總金額 {{ totalPrice }}
      v-btn.mx-auto(color='success' @click='user.checkout' :disabled='!canCheckout') 結帳
</template>

<script setup>
import { reactive, computed } from 'vue'
import Swal from 'sweetalert2'
import { apiAuth } from '@/plugins/axios'
import { useUserStore } from '@/stores/user'

const user = useUserStore()

const cart = reactive([])

const totalPrice = computed(() => {
  return cart.reduce((a, b) => {
    return a + b.product.price * b.quantity
  }, 0)
})
const canCheckout = computed(() => {
  return cart.length > 0 && !cart.some(item => {
    return !item.product.sell
  })
})

const updateCart = async (idx, quantity) => {
  const result = await user.updateCart({ product: cart[idx].product._id, quantity })
  if (result) {
    if (quantity === 0) {
      cart.splice(idx, 1)
    } else {
      cart[idx].quantity = quantity
    }
  }
}

const init = async () => {
  try {
    const { data } = await apiAuth.get('/users/cart')
    cart.push(...data.result)
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
