<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'
import CardList from '@/components/CardList.vue'

  const favorites = ref([])

  onMounted( async () => {
    try {
      const { data } = await axios.get(`https://608262f1778874f6.mokky.dev/favorites?_relations=items`)

      favorites.value = data.map((obj) => obj.item)
    } catch (error) {
      console.log(error)
    }
  })
</script>

<template>
  <h2 class="text-3xl font-bold mb-8">Збережені</h2>

  <CardList :items="favorites" is-favorites />
</template>