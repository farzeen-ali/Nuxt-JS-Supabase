<script setup>
import { createClient } from '@supabase/supabase-js'
const config = useRuntimeConfig()
const supabase = createClient(config.public.supabaseUrl, config.public.supabasePublishableKey)
const instruments = ref([])
const form = ref({ name: ''})

async function getInstruments() {
  const { data } = await supabase.from('instruments').select()
  instruments.value = data
}

async function addInstrument(){
  if(!form.value?.name) return;
  const { error } = await supabase
    .from('instruments')
    .insert([ { name: form.value.name }])
    if(!error){
      form.value.name = ''
      getInstruments();
    } else{
      console.log(error)
    }
}

onMounted(() => {
  getInstruments()
})
</script>

<template>
  <div>
    <form @submit.prevent="addInstrument">
      <input type="text" v-model="form.name" placeholder="Enter instrument name">
      <button type="submit">Add</button>
    </form>
  <ul>
    <li v-for="instrument in instruments" :key="instrument.id">{{ instrument.name }}</li>
  </ul>
    </div>
</template>