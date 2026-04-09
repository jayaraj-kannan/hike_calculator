<template>
  <v-app class="bg-slate-950 text-slate-200">
    <div class="fixed inset-0 z-0 bg-[radial-gradient(ellipse_at_top,_var(--tw-gradient-stops))] from-slate-900 via-[#0a0f1c] to-black"></div>
    <v-main class="relative z-10 w-full min-h-screen">
      <HikeCalculator />
    </v-main>
  </v-app>
</template>

<script lang="ts" setup>
  import HikeCalculator from '@/components/HikeCalculator.vue'
</script>
