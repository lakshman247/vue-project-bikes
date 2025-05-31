<template>
  <div class="min-h-screen flex flex-col items-center justify-start bg-gradient-to-r from-[#f0f4f8] to-[#d9e2ec] p-10">
    <div class="bg-white p-6 rounded-2xl shadow-lg mb-6 flex flex-col md:flex-row md:items-center space-y-4 md:space-y-0 md:space-x-4 w-full max-w-6xl">
      <input
        v-model="searchTerm"
        type="text"
        placeholder="Search by company name..."
        class="flex-1 px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-400"
      />
      <Button label="Search" severity="info"  @click="validateAndSearch()"/>
      <Button label="Reset" severity="info"   @click="resetData()"/>
      <Button label=" Add New Bike" severity="success"  @click="openCanvas = true"/>
    </div>

    <!-- Toast -->
    <div v-if="toast.visible"
      class="fixed top-5 right-5 bg-gray-800 text-white px-4 py-2 rounded shadow-lg transition-opacity"
    >
      {{ toast.message }}
    </div>

    <!-- Table -->
    <div class="overflow-auto rounded-2xl shadow-lg w-full max-w-6xl bg-white">
      <table class="min-w-full text-sm text-left">
        <thead class="bg-gradient-to-r from-blue-700 to-blue-500 text-white text-xs uppercase tracking-wide">
          <tr>
            <th class="px-4 py-3">Id</th>
            <th class="px-4 py-3">Company</th>
            <th class="px-4 py-3">CC</th>
            <th class="px-4 py-3">Price (INR)</th>
            <th class="px-4 py-3">Year</th>
            <th class="px-4 py-3">Mileage</th>
            <th class="px-4 py-3">Colors</th>
            <th class="px-4 py-3">Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-if="isLoading" v-for="i in 5" :key="i" class="border-b">
            <td colspan="8" class="px-4 py-3">
              <div class="w-full h-10 bg-gray-200 animate-pulse rounded"></div>
            </td>
          </tr>

          <tr
            v-else-if="paginatedData.length"
            v-for="(bike, index) in paginatedData"
            :key="bike.id"
            :class="[index % 2 === 0 ? 'bg-blue-50' : 'bg-white', 'border-b hover:bg-blue-100 transition-all']"
          >
            <td class="px-4 py-2 font-semibold">
              {{ (currentPage - 1) * itemsPerPage + index + 1 }}
            </td>
            <td class="px-4 py-2">{{ bike.company }}</td>
            <td class="px-4 py-2">{{ bike.engineCC }}cc</td>
            <td class="px-4 py-2">₹{{ bike.priceINR.toLocaleString() }}</td>
            <td class="px-4 py-2">{{ bike.year }}</td>
            <td class="px-4 py-2">{{ bike.mileage }}</td>
            <td class="px-4 py-2">
              {{ Array.isArray(bike.color) ? bike.color.join(', ') : bike.color }}
            </td>
            <td class="px-4 py-2 flex space-x-2">
              <Button label="Edit" severity="help" @click="editBike(bike)"/>
              <Button label="Delete" severity="danger" @click="deleteBike(bike.id)"/>
            </td>
          </tr>

          <tr v-else class="border-b">
            <td colspan="8" class="px-4 py-3 text-center text-gray-500">
              No data found.
            </td>
          </tr>
        </tbody>
      </table>

      <!-- Pagination -->
      <div v-if="paginatedData.length" class="flex justify-center items-center space-x-2 p-4 border-t bg-white">
          <Button label="Previous" severity="secondary"   @click="changePage(currentPage - 1)"
          :disabled="currentPage === 1"/>
        <Button
          v-for="page in totalPages"
          :key="page"
          @click="changePage(page)"
          :severity=" page === currentPage ? 'info' : 'secondary'"
        >
          {{ page }}
        </Button>
        <Button label="Next" severity="secondary" @click="changePage(currentPage + 1)"
          :disabled="currentPage === totalPages"/>
      </div>
    </div>
    <Drawer  v-model:visible="openCanvas" header="AddNewBike" position="right">
      <template v-if='openCanvas'>
        <AddNewBike  @submitedData='addNewBike' :editBikeObj="editBikeObj"></AddNewBike>
      </template>
    </Drawer>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'
import Button from "primevue/button"
import Drawer from 'primevue/drawer';
import AddNewBike from "./components/AddBike.vue"
const bikesInfo = ref([])
const isLoading = ref(true)
const currentPage = ref(1)
const itemsPerPage = 10
const toast = ref({ visible: false, message: '' })
const searchTerm = ref('')
const filteredData = ref([])
const openCanvas = ref(false)
const editBikeObj = ref({})

const showToast = (msg) => {
  toast.value = { visible: true, message: msg }
  setTimeout(() => (toast.value.visible = false), 3000)
}

const getVehicleDetails = async () => {
  try {
    const res = await fetch('http://localhost:3001/bikes')
    const data = await res.json()
    bikesInfo.value = data
    filteredData.value = data
    showToast('Data fetched successfully')
  } catch (err) {
    console.error(err)
    showToast('Failed to fetch data')
  } finally {
    setTimeout(() => {
      isLoading.value = false
    }, 2000)
  }
}

const validateAndSearch = ()=>{
    if (!searchTerm.value.trim()) return 
  filteredData.value =  bikesInfo.value.filter((bike) =>
    bike.company.toLowerCase().includes(searchTerm.value.toLowerCase())
  )
}

const resetData = () => {
  if(searchTerm.value){
    searchTerm.value = ''
    currentPage.value = 1
    showToast('Reset to full data')
    filteredData.value =  bikesInfo.value
  }
}
const totalPages = computed(() =>
  Math.ceil(filteredData.value.length / itemsPerPage)
)

const paginatedData = computed(() =>
  filteredData.value.slice(
    (currentPage.value - 1) * itemsPerPage,
    currentPage.value * itemsPerPage
  )
)

const changePage = (page) => {
  if (page >= 1 && page <= totalPages.value) {
    currentPage.value = page
  }
}

const editBike = (bike) => {
   openCanvas.value =  true
  editBikeObj.value = bike
}

const deleteBike = async (id) => {
  try {
    const response = await fetch(`http://localhost:3001/bikes/${id}`, {
      method: 'DELETE',
      headers: {
        'Content-Type': 'application/json'
      }
    })

    if (!response.ok) {
       showToast('something went wrong')
    }
    showToast('Bike deleted successfully')
  } catch (error) {
    console.error('Error:', error)
  }
}

 const addNewBike = async (bikeObj) => {
  showToast('Adding new bike...')
  openCanvas.value = false
    const request = bikeObj.id ? `http://localhost:3001/bikes/${bikeObj.id}` : 'http://localhost:3001/bikes'
    const type = bikeObj.id ? 'PUT' :'POST' 
  try {
    const response = await fetch(request, {
      method: type,
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(bikeObj)
    })

    if (!response.ok) {
      throw new Error(`Failed to add bike: ${response.statusText}`)
    }
    showToast(`Bike "${result.company}" added successfully`)

    // Optional: Refresh data list
    await getVehicleDetails()
  } catch (err) {
    console.error('Error:', err)
    showToast('Error adding bike. Please try again.')
  }
}


onMounted(() => {
  getVehicleDetails()
})
</script>

<style scoped>
</style>
