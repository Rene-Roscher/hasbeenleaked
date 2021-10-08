<template>
  <div>
    <svg class="w-6 h-6 flex-none mt-0.5" fill="none" viewBox="0 0 24 24" v-if="type === 'danger'">
      <circle cx="12" cy="12" r="12" fill="#FECDD3"></circle>
      <path d="M8 8l8 8M16 8l-8 8" stroke="#B91C1C" stroke-width="2"></path>
    </svg>
    <svg class="w-6 h-6 flex-none mt-0.5" fill="none" viewBox="0 0 24 24" v-else-if="type === 'success'">
      <circle cx="12" cy="12" r="12" fill="#A7F3D0"></circle>
      <path d="M18 8l-8 8-4-4" stroke="#047857" stroke-width="2"></path>
    </svg>
  </div>
</template>

<script>
export default {
  props: {
    type: {
      type: String,
      default: 'success',
    }
  }
}
</script>