<script setup lang="ts">
import axios from 'axios'
import { ref, reactive, onMounted, watch } from 'vue';
import Card from './components/Card.vue'

interface Options {
  id: Number,
  name: String,
  status: String,
  image: String,
  species: String,
  location: {
    name: String,
  },
  episode: String
}

const items = ref<Options[]>([]);
const selected = ref(1)
const numOfPages = ref(0)
const changed = ref(false)

const filters = reactive({
    status: '',
    name: ''
})

const onChangePage = (event: any) => {
    selected.value = event.target.value
}

const onChangeName = (event: any) => {
    filters.name = event.target.value
    changed.value = true
}

const onChangeStatus = (event: any) => {
    filters.status = event.target.value
    changed.value = true
}

const fetchData = async () => {
  if (changed.value == true) {
    selected.value = 1
  }
  const query = ref('?')
  if (filters.name != '') {
    query.value += `name=${filters.name}`
  }
  if (filters.status != '') {
    query.value += `&status=${filters.status}`
  }
  if (query.value == '?') {
    query.value += `page=${selected.value}`
  } else {
    query.value += `&page=${selected.value}`
  }
  changed.value = false
  const resp = await axios.get(`https://rickandmortyapi.com/api/character/${query.value}`)
  let data = resp.data
  numOfPages.value = resp.data.info.pages
  
  for (const item in data.results) {
    const resp = await axios.get(data.results[item].episode[0])
    data.results[item].episode = resp.data.name
  }

  items.value = data.results
  return data.results
}

onMounted(async () =>
  await fetchData()
)
watch(selected, fetchData)
</script>

<template>
  <div id="wrapper">
    <section id="filter">
      <select v-model="selected" @change="onChangePage" name="Номер страницы">
        <option v-for="i in numOfPages" :value="i" :key="i">{{ i }}</option>
      </select>
      <input @input="onChangeName" type="text" placeholder="Введите имя..."/>
      <select @change="onChangeStatus" id="">
        <option value="">all</option>
        <option value="alive">alive</option>
        <option value="dead">dead</option>
        <option value="unknown">unknown</option>
      </select>
      <button @click="fetchData">Применить</button>
    </section>
    <div id="content">
      <Card
        v-for="item in items"
        :key="Number(item.id)"
        :image="String(item.image)"
        :name="String(item.name)"
        :species="String(item.species)"
        :status="String(item.status)"
        :location="String(item.location.name)"
        :episode="String(item.episode)"
      >
      </Card>
    </div>
  </div>
</template>
