<script setup>
import DrawerHead from '@/components/DrawerHead.vue'
import CartItemList from '@/components/CartItemList.vue'
import { computed, inject, ref } from 'vue'
import InfoBlock from '@/components/InfoBlock.vue'
import axios from 'axios'

const props = defineProps({
  totalPrice: Number,
  vatPrice: Number
})

const isCreating = ref(false)
const orderId = ref(null)

const { cart, closeDrawer } = inject('cart')

const createOrder = async () => {
  try {
    isCreating.value = true
    const { data } = await axios.post(`https://608262f1778874f6.mokky.dev/orders`, {
      items: cart.value,
      totalPrice: props.totalPrice.value
    })
    cart.value = []
    orderId.value = data.id
  } catch (error) {
    console.log(error)
  } finally {
    isCreating.value = false
  }
}

const cartIsEmpty = computed(() => cart.value.length === 0)
const buttonDisabled = computed(() => isCreating.value || cartIsEmpty.value)

</script>

<template>
  <div class="fixed top-0 left-0 h-full w-full bg-black z-10 opacity-70"></div>
  <div class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-10">
    <DrawerHead />



    <div v-if="!totalPrice || orderId" class="flex h-full items-center">
      <InfoBlock
        v-if="!totalPrice & !orderId"
        title="Замовлень покищо немає"
        description="Додайте хоча б один товар, щоб зробити замовлення."
        image-url="/package-icon.png"
      />

      <InfoBlock
        v-if="orderId"
        title="Ваше замовлення оформлено!"
        :description="`Ваше замовлення №${orderId}. Наш менеджер звя'жеться з вами найближчим часом для підтвердження замовлення`"
        image-url="/order-success-icon.png"
      />
    </div>

    <div v-else>
      <CartItemList />

      <div class="flex flex-col gap-4 mt-7">

        <div class="flex gap-2">
          <span>Разом</span>
          <div class="flex-1 border-b border-dashed"></div>
          <b>{{ totalPrice }} грн.</b>
        </div>

        <div class="flex gap-2">
          <span>Податок 5%</span>
          <div class="flex-1 border-b border-dashed"></div>
          <b>{{ vatPrice }} грн.</b>
        </div>

        <button
          :disabled="buttonDisabled"
          @click="createOrder"
          class=" mt-4 bg-lime-500 w-full rounded-xl py-4 text-white hover:bg-lime-600 active:bg-lime-700 transition disabled:bg-slate-300 cursor-pointer">
          Оформити замовлення
        </button>
      </div>
    </div>
  </div>
</template>
