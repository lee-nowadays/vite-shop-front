<template lang="pug">
v-card.product-card
  v-img(:src='product.image')
  v-card-title
    router-link(:to="'/product/' + product._id") {{ product.name }}
  v-card-subtitle ${{ product.price }}
  v-card-text(style='white-space: pre;') {{ product.description }}
  v-card-actions
    v-btn(color='primary' prepend-icon='mdi-cart' @click='addCart({ product: product._id, quantity: 1 })') 加入購物車
</template>

<script setup>
import { useUserStore } from '@/stores/user'
const user = useUserStore()
const { addCart } = user
defineProps(['product'])
// defineProps({
//   product: {
//     type: Object,
//     required: true,
//     default () {
//       return {
//         _id: '',
//         name: '',
//         price: 0,
//         category: '',
//         sell: false,
//         image: '',
//         description: ''
//       }
//     }
//   }
// })
</script>
