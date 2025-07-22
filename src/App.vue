<template>
  <div style="width: 1000px; margin: 0 auto">
    <BaseSelect :loading="isFetching" @load-more="fetchNextPage" :options></BaseSelect>
  </div>
  <pre></pre>
</template>

<script setup lang="ts">
import { useInfiniteQuery } from '@tanstack/vue-query'
import BaseSelect from './components/BaseSelect.vue'
import { computed, ref } from 'vue'
const limit = ref(50)
const options = computed(() => {
  return data.value?.pages?.flatMap((page) =>
    page.projects.map((p) => ({ label: p.title?.uz, value: p.id })),
  )
})
// API funksiyasi
const fetchProducts = async ({ pageParam = 0 }) => {
  const response = await fetch(
    `https://api.gpp.miit.uz/api/projects/?page=${pageParam}&limit=${limit.value}&action=index`,
  )

  if (!response.ok) {
    throw new Error('Loyihalarni yuklashda xatolik')
  }

  const data = await response.json()
  return {
    projects: data.projects,
    total: data.total_count,
    currentPage: pageParam,
    hasMore: pageParam * limit.value < data.total_count,
  }
}

// Infinite Query
const { data, fetchNextPage, isFetching } = useInfiniteQuery({
  queryKey: ['products'],
  queryFn: fetchProducts,
  initialPageParam: 1,
  getNextPageParam: (lastPage) => {
    // Agar yana mahsulotlar bo'lsa, keyingi sahifa raqamini qaytaradi
    return lastPage.hasMore ? lastPage.currentPage + 1 : undefined
  },
  staleTime: 5 * 60 * 1000, // 5 daqiqa
  refetchOnWindowFocus: false,
})
</script>
