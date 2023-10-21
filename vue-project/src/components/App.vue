<template>
  <div id="#app">
    <span>Місто</span>
    <select v-model="selectedCity">
      <option
        v-for="(city, index) in cities"
        :key="index"
        :value="city.description"
      >
        {{ city.description }}
      </option>
    </select>
    <span>Відділення</span>
    <select v-model="selectedBranch">
      <option
        v-for="(branch, index) in branches"
        :key="index"
        :value="branch.description"
      >
        {{ branch.description }}
      </option>
    </select>
  </div>
</template>

<script>
import { ref, onMounted, watch } from 'vue'
import axios from 'axios'

export default {
  setup () {
    const cities = ref([])
    const selectedCity = ref('')
    const branches = ref([])
    const selectedBranch = ref('')

    onMounted(() => {
      axios
        .post('https://api.novaposhta.ua/v2.0/json/', {
          apiKey: 'f3b7bb3dae9253f3c866298f443f7a25',
          modelName: 'Address',
          calledMethod: 'getCities',
          methodProperties: {}
        })
        .then(response => {
          cities.value = response.data.data.map(city => ({
            description: city.Description
          }))
        })
    })

    watch(selectedCity, newCity => {
      if (newCity) {
        axios
          .post('https://api.novaposhta.ua/v2.0/json/', {
            apiKey: 'f3b7bb3dae9253f3c866298f443f7a25',
            modelName: 'AddressGeneral',
            calledMethod: 'getWarehouses',
            methodProperties: {
              CityName: newCity
            }
          })
          .then(response => {
            branches.value = response.data.data.map(branch => ({
              description: branch.Description
            }))
          })
      } else {
        branches.value = []
      }
    })

    return {
      cities,
      selectedCity,
      branches,
      selectedBranch
    }
  }
}
</script>

<style scoped>
select {
  width: 200px;
  padding: 10px;
  margin: 10px;
}
</style>
