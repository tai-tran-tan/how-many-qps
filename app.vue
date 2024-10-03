<template>
  <div>
    <Panel header="QPS calculator | How many QPS is it?">
      <p class="hidden md:block m-0">
        Input Requests count and Period then the average traffic count for different time spans will be calculated for you.
      </p>
    </Panel>
    <Divider style="margin-bottom: 2rem;"/>
    <InputGroup>
      <FloatLabel>
        <InputNumber v-model="count" inputId="count" :min="0" :max="Number.MAX_SAFE_INTEGER" />
        <label for="count">Requests count</label>
      </FloatLabel>
      <FloatLabel>
        <InputText v-model.trim="period" placeholder="3wk 4d 5h 6m 7s 8ms" inputId="period" :invalid="invalid" />
        <label for="period">Period</label>
      </FloatLabel>
    </InputGroup>
    
    <DataTable :value="calculations">
      <Column field="count" header="Average"></Column>
      <Column field="unit" header="Unit">
      </Column>
    </DataTable>
  </div>
</template>
<script setup lang="ts">
import parse from 'parse-duration'
const count = useState('count', () => 10_000)
const period = useState('period', () => '3w 4d 5h 6m 7s 8ms')
const UNITS = [
  { unit: 'ms', label: 'Milisecond' },
  { unit: 's', label: 'Second' },
  { unit: 'm', label: 'Minute' },
  { unit: 'h', label: 'Hour' },
  { unit: 'd', label: 'Day' },
  { unit: 'w', label: 'Week' }
]
const calculations = computed(() => UNITS.map(p => ({
  'count': calculate(count.value, p.unit),
  'unit': p.label
}))
)
const invalid = computed(() => {
  const invalidRemain = period.value.replace(/(\d+)(\.\d+)?(w|d|h|s|ms|m)(\s?)/g, '')
  console.log(invalidRemain)
  return invalidRemain != undefined && invalidRemain.length > 0
})
function calculate(cnt: number, unit: string) {
  if (parse(period.value, unit) === 0 || invalid.value) return '-';

  return (cnt / parse(period.value, unit))
    .toLocaleString('en-US', { minimumFractionDigits: 1, maximumFractionDigits: 2 })
}
</script>

<style>
div[id=__nuxt] {
  max-width: 100ch;
  min-width: 50ch;
  margin:auto;
}
</style>