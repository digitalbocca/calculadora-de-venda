<template>
  <div class="app-container">
    <h1 class="app-container__title swing-in-top-fwd">
      {{ appName }}
    </h1>
    <currency-input
      class="giga-input swing-in-top-fwd"
      v-model="mediumPrice"
      :options="config"
    />
    <div
      class="app-container__result-table"
      :class="isVisible"
    >
      <h2 class="app-container__sub-title">
        Valores
      </h2>
      <table class="results-table">
        <thead>
          <tr>
            <th>%</th>
            <th>Ágio</th>
            <th>Total</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="result in results"
            :key="result.base"
            class="mt-2 font-monospace"
          >
            <td>{{ result.label }}</td>
            <td>{{ result.agio }}</td>
            <td>{{ result.total }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup>

import { computed, ref } from 'vue'

import CurrencyInput from '@/components/currency-input.vue'

const config = {
  locale: 'pt-BR',
  currency: 'BRL',
  currencyDisplay: 'symbol',
  precision: {
    min: 2,
    max: 2
  },
  hideCurrencySymbolOnFocus: true,
  hideGroupingSeparatorOnFocus: true,
  hideNegligibleDecimalDigitsOnFocus: false,
  autoDecimalDigits: true,
  valueScaling: 'precision',
  useGrouping: true,
  accountingSign: true
}

const appName = 'Calculadora'

const steps = [
  {
    label: '5%',
    base: 5
  },
  {
    label: '6%',
    base: 6
  },
  {
    label: '7%',
    base: 7
  },
  {
    label: '8%',
    base: 8
  },
  {
    label: '9%',
    base: 9
  },
  {
    label: '10%',
    base: 10
  }
]

const mediumPrice = ref(0)

const results = computed(() => {
  return steps.map(step => {
    return {
      ...step,
      ...calculate(step.base)
    }
  })
})

const calculate = step => {
  const agio = (mediumPrice.value * step) / 100
  const value = (mediumPrice.value + agio) / 100

  const format = value => {
    return Intl.NumberFormat('pt-BR', {
      style: 'currency',
      currency: 'BRL'
    }).format(value)
  }

  return {
    agio: format(agio / 100),
    total: format(value)
  }
}

const isVisible = computed(() => {
  return mediumPrice.value > 0 ? 'visible' : 'hidden'
})


</script>

<style lang="sass" scoped>

.app-container
  @apply md:container md:mx-auto
  @apply flex flex-col items-center justify-center gap-8

  &__title
    @apply text-center uppercase text-5xl font-bold
    @apply mt-8

  &__result-table
    @apply flex flex-col justify-center items-center gap-8

  &__sub-title
    @apply text-center uppercase text-3xl font-bold

  .giga-input
    @apply text-5xl font-mono text-right text-slate-900
    @apply py-3 px-6
    @apply border-none rounded-lg
  
    &:focus
      @apply outline-none border-none shadow-none

  .results-table
    @apply table-auto
    @apply bg-white text-slate-900
    @apply border-none rounded-lg

    thead
      @apply uppercase

    tr th
      @apply p-6

    tr td
      @apply px-6 py-3

  .visible
    -webkit-animation: swing-in-top-fwd 0.5s cubic-bezier(0.175, 0.885, 0.320, 1.275) both
    animation: swing-in-top-fwd 0.5s cubic-bezier(0.175, 0.885, 0.320, 1.275) both

  .hidden
    -webkit-animation: swing-out-top-bck 0.45s cubic-bezier(0.600, -0.280, 0.735, 0.045) both
    animation: swing-out-top-bck 0.45s cubic-bezier(0.600, -0.280, 0.735, 0.045) both

</style>