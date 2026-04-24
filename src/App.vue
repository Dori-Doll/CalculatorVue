<script setup lang="ts">
import { computed, onBeforeUnmount, ref, watch } from 'vue'

type Action = 'add' | 'subtract' | 'multiply' | 'divide'

const num1 = ref<number>(0)
const num2 = ref<number>(0)
const action = ref<Action>('add')
const result = ref<number | null>(null)

const calculations: Record<Action, (x: number, y: number) => number> = {
  add: (x, y) => x + y,
  subtract: (x, y) => x - y,
  multiply: (x, y) => x * y,
  divide: (x, y) => x / y,
}

const operatorOptions = [
  { label: 'Додавання (+)', value: 'add' },
  { label: 'Віднімання (-)', value: 'subtract' },
  { label: 'Множення (*)', value: 'multiply' },
  { label: 'Ділення (/)', value: 'divide' },
]

const hasZeroResult = computed(() => result.value === 0)

function calculate() {
  result.value = calculations[action.value](num1.value, num2.value)
}

watch(hasZeroResult, (isZero) => {
  document.body.classList.toggle('blink-bg', isZero)
})

onBeforeUnmount(() => {
  document.body.classList.remove('blink-bg')
})
</script>

<template>
  <div class="calculator-shell">
    <UCard class="calculator-card">
      <template #header>
        <div class="title-block">
          <p class="eyebrow">Nuxt UI</p>
          <h1>КАЛЬКУЛЯТОР</h1>
        </div>
      </template>

      <div class="fields">
        <label for="num1">Введіть перше число</label>
        <UInput id="num1" v-model.number="num1" type="number" size="xl" />

        <label for="operator">Оберіть дію тут</label>
        <select id="operator" v-model="action" class="operator-select">
          <option v-for="option in operatorOptions" :key="option.value" :value="option.value">
            {{ option.label }}
          </option>
        </select>

        <label for="num2">Введіть друге число</label>
        <UInput id="num2" v-model.number="num2" type="number" size="xl" />
      </div>

      <div class="actions">
        <UButton size="xl" color="primary" variant="solid" @click="calculate">
          РАХУВАТИ
        </UButton>
      </div>

      <p class="result" :class="{ zero: hasZeroResult }">
        {{ result === null ? 'Відповідь: —' : `Відповідь: ${result}` }}
      </p>
    </UCard>
  </div>
</template>

<style scoped>
:global(body) {
  margin: 0;
  min-height: 100vh;
  background:
    radial-gradient(circle at top, rgba(255, 255, 255, 0.14), transparent 34%),
    linear-gradient(180deg, #f7f7f7 0%, #ececec 100%);
  transition: background-color 0.5s linear;
}

:global(body.blink-bg) {
  animation: bodyBlink 1s infinite;
}

@keyframes bodyBlink {
  0%,
  100% {
    background-color: #ffffff;
  }

  50% {
    background-color: #000000;
  }
}

.calculator-shell {
  min-height: 100vh;
  display: grid;
  place-items: center;
  padding: 24px;
}

.calculator-card {
  width: min(720px, 100%);
  border-radius: 28px;
  backdrop-filter: blur(18px);
  box-shadow: 0 24px 60px rgba(0, 0, 0, 0.18);
}

.title-block {
  display: grid;
  gap: 6px;
  text-align: center;
}

.eyebrow {
  margin: 0;
  letter-spacing: 0.22em;
  text-transform: uppercase;
  font-size: 0.75rem;
  opacity: 0.65;
}

h1 {
  margin: 0;
  font-size: clamp(2rem, 5vw, 3.5rem);
}

.fields {
  display: grid;
  gap: 14px;
}

label {
  font-size: 1.8rem;
  line-height: 1.1;
}

.operator-select {
  width: 100%;
  border-radius: 14px;
  border: 1px solid rgba(0, 0, 0, 0.12);
  background: #fff;
  padding: 16px 18px;
  font: inherit;
}

.actions {
  margin-top: 24px;
  display: flex;
  justify-content: center;
}

.result {
  margin: 24px 0 0;
  text-align: center;
  font-size: clamp(1.5rem, 3vw, 2.25rem);
  font-weight: 700;
}

.result.zero {
  color: #dc2626;
}

@media (max-width: 640px) {
  .calculator-card {
    border-radius: 20px;
  }

  label {
    font-size: 1.35rem;
  }
}
</style>
