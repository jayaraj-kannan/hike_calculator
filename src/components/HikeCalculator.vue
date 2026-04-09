<template>
  <v-container class="fill-height flex items-center justify-center p-4">
    <v-card
      class="mx-auto my-12 relative overflow-hidden backdrop-blur-xl bg-slate-900/80 border border-slate-700/50 shadow-[0_0_40px_rgba(59,130,246,0.15)]"
      max-width="550"
      width="100%"
      elevation="10"
      rounded="xl"
    >
      <div class="absolute inset-0 bg-gradient-to-br from-indigo-500/10 via-transparent to-teal-500/10 pointer-events-none"></div>

      <v-card-item class="pb-2 pt-6">
        <template v-slot:title>
          <div class="text-3xl font-extrabold text-transparent bg-clip-text bg-gradient-to-r from-blue-400 to-teal-300 text-center tracking-tight mb-1">
            Salary Hike Calculator
          </div>
        </template>
        <template v-slot:subtitle>
          <div class="text-center text-slate-400 font-medium">
            Plan your next career move intuitively.
          </div>
        </template>
        <template v-slot:append>
          <v-btn
            icon="mdi-refresh"
            variant="text"
            color="grey-lighten-1"
            size="small"
            class="hover:rotate-180 transition-transform duration-500 hover:text-white"
            @click="resetForm"
            title="Reset form"
          ></v-btn>
        </template>
      </v-card-item>

      <v-card-text class="mt-4 px-6 sm:px-8">
        <div class="space-y-6">
          <!-- Current Settings -->
          <div class="group">
            <v-text-field
              v-model="currentSalary"
              prefix="₹"
              label="Current Annual Salary"
              variant="outlined"
              type="number"
              hide-details
              base-color="blue-grey-darken-1"
              color="primary"
              density="comfortable"
              class="group-hover:drop-shadow-md transition-all duration-300"
              @update:modelValue="onParamChange"
            ></v-text-field>
          </div>

          <v-divider class="my-4 border-slate-700"></v-divider>

          <v-row>
            <v-col cols="12" sm="6" >
              <div class="group">
                <v-text-field
                  v-model="hikePercentage"
                  suffix="%"
                  label="Expected Hike"
                  variant="outlined"
                  type="number"
                  hide-details
                  base-color="blue-grey-darken-1"
                  color="teal-accent-4"
                  density="comfortable"
                  class="group-hover:drop-shadow-md transition-all duration-300"
                  @update:modelValue="calculateFromPercentage"
                ></v-text-field>
              </div>
            </v-col>
            <v-col cols="12" sm="6">
              <div class="group relative">
                <v-text-field
                  v-model="targetSalary"
                  prefix="₹"
                  label="Target Annual Salary"
                  variant="outlined"
                  type="number"
                  hide-details
                  base-color="blue-grey-darken-1"
                  color="indigo-accent-2"
                  density="comfortable"
                  class="group-hover:drop-shadow-md transition-all duration-300"
                  @update:modelValue="calculateFromTarget"
                ></v-text-field>
                <div class="absolute -inset-1 rounded-lg blur opacity-20 bg-gradient-to-r from-teal-400 to-indigo-500 pointer-events-none group-focus-within:opacity-40 transition duration-500"></div>
              </div>
            </v-col>
          </v-row>
        </div>

        <!-- Output Results -->
        <v-expand-transition>
          <div v-if="isValid" class="mt-8">
            <v-card class="bg-slate-800/60 border border-slate-700/50 shadow-inner rounded-xl backdrop-blur-md">
              <v-card-text class="py-5 px-6">
                <div class="grid grid-cols-2 gap-4">
                  <!-- Hike Amount -->
                  <div class="col-span-2 sm:col-span-1 rounded-lg bg-teal-500/10 p-3 border border-teal-500/20">
                    <div class="text-xs text-teal-300 font-semibold uppercase tracking-wider mb-1">Annual Hike</div>
                    <div class="text-2xl font-bold text-teal-100">{{ formatCurrency(annualHike) }}</div>
                  </div>
                  
                  <!-- Target Monthly -->
                  <div class="col-span-2 sm:col-span-1 rounded-lg bg-indigo-500/10 p-3 border border-indigo-500/20">
                    <div class="text-xs text-indigo-300 font-semibold uppercase tracking-wider mb-1">New Monthly</div>
                    <div class="text-2xl font-bold text-indigo-100">{{ formatCurrency(targetMonthly) }}</div>
                  </div>

                  <!-- Details Row -->
                  <div class="col-span-2 mt-2 pt-3 border-t border-slate-700/60">
                    <div class="flex justify-between items-center text-sm">
                      <span class="text-slate-400">Current Monthly:</span>
                      <span class="font-medium text-slate-200">{{ formatCurrency(currentMonthly) }}</span>
                    </div>
                    <div class="flex justify-between items-center text-sm mt-1">
                      <span class="text-slate-400">Monthly Hike:</span>
                      <span class="font-medium text-green-400">+{{ formatCurrency(monthlyHike) }}</span>
                    </div>
                  </div>
                </div>
              </v-card-text>
            </v-card>
          </div>
        </v-expand-transition>
      </v-card-text>
    </v-card>
  </v-container>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'

const currentSalary = ref<number | string | null>(null)
const hikePercentage = ref<number | string | null>(null)
const targetSalary = ref<number | string | null>(null)

const resetForm = () => {
  currentSalary.value = null
  hikePercentage.value = null
  targetSalary.value = null
}

const calculateFromPercentage = () => {
  if (currentSalary.value && hikePercentage.value !== null && hikePercentage.value !== '') {
    const val = Number(currentSalary.value) * (1 + Number(hikePercentage.value) / 100)
    targetSalary.value = Number(val.toFixed(0))
  } else if (!hikePercentage.value) {
    targetSalary.value = null
  }
}

const calculateFromTarget = () => {
  if (currentSalary.value && targetSalary.value !== null && targetSalary.value !== '') {
    const percent = ((Number(targetSalary.value) - Number(currentSalary.value)) / Number(currentSalary.value)) * 100
    hikePercentage.value = Number(percent.toFixed(2))
  } else if (!targetSalary.value) {
    hikePercentage.value = null
  }
}

const onParamChange = () => {
  // Try to keep percentage constant and update target if both existed, 
  // else try to keep target and update percentage
  if (currentSalary.value && hikePercentage.value !== null && hikePercentage.value !== '') {
     calculateFromPercentage()
  } else if (currentSalary.value && targetSalary.value !== null && targetSalary.value !== '') {
     calculateFromTarget()
  }
}

const isValid = computed(() => {
  return currentSalary.value && targetSalary.value && Number(targetSalary.value) >= Number(currentSalary.value)
})

const annualHike = computed(() => {
  if (!isValid.value) return 0
  return Number(targetSalary.value) - Number(currentSalary.value)
})

const currentMonthly = computed(() => {
  if (!currentSalary.value) return 0
  return Number(currentSalary.value) / 12
})

const targetMonthly = computed(() => {
  if (!targetSalary.value) return 0
  return Number(targetSalary.value) / 12
})

const monthlyHike = computed(() => {
  if (!isValid.value) return 0
  return annualHike.value / 12
})

const formatCurrency = (val: number) => {
  return new Intl.NumberFormat('en-IN', {
    style: 'currency',
    currency: 'INR',
    maximumFractionDigits: 0
  }).format(val)
}
</script>

<style scoped>
/* Scoped overrides to ensure Vuetify components fit the dark glass theme */
:deep(.v-field__input) {
  color: #e2e8f0; /* slate-200 */
}
:deep(.v-label) {
  color: #94a3b8; /* slate-400 */
}
</style>
