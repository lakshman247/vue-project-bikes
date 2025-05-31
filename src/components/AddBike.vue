<template>
  <div class="max-w-xl mx-auto p-6 bg-white rounded-xl">
    <div class="grid gap-4">
      <!-- Company -->
      <div>
        <label class="block mb-1 font-medium">Company</label>
        <InputText v-model="form.company" class="w-full" :class="{ 'p-invalid': errors.company }" />
        <small v-if="errors.company" class="text-red-500">{{ errors.company }}</small>
      </div>

      <!-- Engine CC -->
      <div>
        <label class="block mb-1 font-medium">Engine CC</label>
        <InputNumber v-model="form.engineCC" class="w-full" :class="{ 'p-invalid': errors.engineCC }" />
        <small v-if="errors.engineCC" class="text-red-500">{{ errors.engineCC }}</small>
      </div>

      <!-- Price INR -->
      <div>
        <label class="block mb-1 font-medium">Price (INR)</label>
        <InputNumber v-model="form.priceINR" class="w-full" mode="currency" currency="INR" locale="en-IN"
          :class="{ 'p-invalid': errors.priceINR }" />
        <small v-if="errors.priceINR" class="text-red-500">{{ errors.priceINR }}</small>
      </div>

      <!-- Year -->
      <div>
        <label class="block mb-1 font-medium">Year</label>
        <InputNumber v-model="form.year" class="w-full" :min="2000" :max="2100"
          :class="{ 'p-invalid': errors.year }" />
        <small v-if="errors.year" class="text-red-500">{{ errors.year }}</small>
      </div>

      <!-- Mileage -->
      <div>
        <label class="block mb-1 font-medium">Mileage (kmpl)</label>
        <InputText v-model="form.mileage" class="w-full" :class="{ 'p-invalid': errors.mileage }" />
        <small v-if="errors.mileage" class="text-red-500">{{ errors.mileage }}</small>
      </div>

      <!-- Colors -->
      <div>
        <label class="block mb-1 font-medium">Colors</label>
        <InputText v-model="form.color" class="w-full" :class="{ 'p-invalid': errors.mileage }" />
        <small v-if="errors.color" class="text-red-500">{{ errors.color }}</small>
      </div>

      <!-- Submit Button -->
      <Button label="Submit" class="mt-4" @click="handleSubmit()" />
    </div>
  </div>
</template>

<script setup>
import { ref, computed,defineEmits,defineProps, onMounted } from 'vue'
import InputText from 'primevue/inputtext'
import InputNumber from 'primevue/inputnumber'
import Button from 'primevue/button'
const props = defineProps(['editBikeObj'])
const emit = defineEmits(['submitedData'])
const form = ref({
  company: '',
  engineCC: null,
  priceINR: null,
  year: null,
  mileage: null,
  color: ''
})

const errors = ref({})

const validateForm = () => {
  errors.value = {}
  if (!form?.value?.company?.trim()) errors.value.company = 'Company is required'
  if (!form?.value?.engineCC || form?.value?.engineCC <= 0) errors.value.engineCC = 'Valid CC required'
  if (!form?.value?.priceINR || form?.value?.priceINR <= 0) errors.value.priceINR = 'Valid price required'
  if (!form?.value?.year || form?.value?.year < 2000 || form.value.year > 2100) errors.value.year = 'Enter valid year'
  if (!form?.value?.mileage) errors.value.mileage = 'Mileage required'
  if (!form?.value?.color) errors.value.color = 'At least one color required'

  return Object.keys(errors.value).length === 0
}
const handleSubmit = () => {
  if (validateForm()) {
    emit('submitedData', form.value)
    console.log('Submitted:', form.value)

  }
}
onMounted(() => {
    if(props?.editBikeObj){
        form.value = {...props.editBikeObj}
    }
})

</script>
