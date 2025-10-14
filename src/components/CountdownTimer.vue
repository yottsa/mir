<template>
  <div class="container">
    <div v-if="ended" class="ended">{{ endMessage }}</div>
    <div v-else class="countdown">
      <div class="card" v-if="showDays">
        <div class="num">{{ days }}</div>
        <div class="label">{{ daysLabel }}</div>
      </div>
      <div class="card" v-if="showHours">
        <div class="num">{{ hoursPadded }}</div>
        <div class="label">{{ hoursLabel }}</div>
      </div>
      <div class="card" v-if="showMinutes">
        <div class="num">{{ minutesPadded }}</div>
        <div class="label">{{ minutesLabel }}</div>
      </div>
      <div class="card" v-if="showSeconds">
        <div class="num">{{ secondsPadded }}</div>
        <div class="label">{{ secondsLabel }}</div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, onBeforeUnmount, defineProps } from 'vue'

const props = defineProps<{ endDate: string; endMessage: string }>()

const endDate = new Date(props.endDate)
const endMessage = ref(props.endMessage)

const hoursSet = new Set([2, 3, 4])
const secondsSet = new Set([2, 3, 4])

const days = ref(0)
const hours = ref(0)
const minutes = ref(0)
const seconds = ref(0)
const ended = ref(false)

const update = () => {
  const now = new Date()
  let diffMs = endDate.getTime() - now.getTime()
  if (diffMs <= 0) {
    ended.value = true
    days.value = hours.value = minutes.value = seconds.value = 0
    return
  }

  const msInSecond = 1000
  const msInMinute = 60 * msInSecond
  const msInHour = 60 * msInMinute
  const msInDay = 24 * msInHour

  days.value = Math.floor(diffMs / msInDay)
  diffMs %= msInDay

  hours.value = Math.floor(diffMs / msInHour)
  diffMs %= msInHour

  minutes.value = Math.floor(diffMs / msInMinute)
  diffMs %= msInMinute

  seconds.value = Math.floor(diffMs / msInSecond)
}

let timerId: number | null = null

onMounted(() => {
  update()
  timerId = setInterval(update, 1000)
})

onBeforeUnmount(() => {
  if (timerId) clearInterval(timerId)
})

const hoursPadded = computed(() => String(hours.value).padStart(2, '0'))
const minutesPadded = computed(() => String(minutes.value).padStart(2, '0'))
const secondsPadded = computed(() => String(seconds.value).padStart(2, '0'))

const showDays = computed(() => days.value > 0)
const showHours = computed(() => days.value > 0 || hours.value > 0)
const showMinutes = computed(() => days.value > 0 || hours.value > 0 || minutes.value > 0)
const showSeconds = computed(() => true)

const daysLabel = computed(() => (days.value % 10 == 1 && days.value % 100 != 11 ? 'dan' : 'dana'))
const hoursLabel = computed(() =>
  (hours.value % 10 == 1 && hours.value % 100 != 100) || hoursSet.has(hours.value)
    ? 'sata'
    : 'sati',
)
const minutesLabel = computed(() =>
  minutes.value % 10 == 1 && minutes.value % 100 != 11 ? 'minut' : 'minuta',
)
const secondsLabel = computed(() =>
  seconds.value % 10 == 1 && seconds.value % 100 != 11
    ? 'sekund'
    : secondsSet.has(seconds.value)
      ? 'sekunde'
      : 'sekundi',
)
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
  padding: 1rem;
}

.countdown {
  display: flex;
  gap: 1rem;
  align-items: center;
  justify-content: center;
  flex-wrap: wrap;
}

.card {
  padding: 1rem 1.25rem;
  border-radius: 12px;
  box-shadow: 0 6px 24px rgba(0, 0, 0, 0.12);
  min-width: 80px;
  text-align: center;
  background: #fff;
}

.num {
  font-size: 2.25rem;
  font-weight: 700;
  line-height: 1;
}

.label {
  font-size: 0.85rem;
  color: #555;
  margin-top: 0.35rem;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

.ended {
  font-size: 1.5rem;
  color: #d00;
  font-weight: 600;
}

/* Mobile grid layout */
@media (max-width: 600px) {
  .countdown {
    gap: 0.6rem;
  }

  .num {
    font-size: 1.5rem;
  }

  .card {
    padding: 0.6rem 1rem;
    min-width: 50px;
  }
}
</style>
