<script setup lang="ts">
import { Select } from 'ant-design-vue'
const { loading } = defineProps<{
  options: { label: string; value: number }[]
  loading?: boolean
}>()
const emit = defineEmits(['loadMore'])
const handleScroll = (e: UIEvent) => {
  const target = e.target as HTMLDivElement | null
  if (target && target.scrollTop + target.clientHeight >= target.scrollHeight) {
    if (loading) return
    emit('loadMore')
  }
}
</script>

<template>
  <Select :loading show-search @popup-scroll="handleScroll" :options style="width: 100%"></Select>
</template>

<style scoped></style>
