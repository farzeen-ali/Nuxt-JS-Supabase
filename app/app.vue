<script setup>
import { createClient } from '@supabase/supabase-js'
const config = useRuntimeConfig()
const supabase = createClient(config.public.supabaseUrl, config.public.supabasePublishableKey)
const instruments = ref([])
const form = ref({ name: ''})

const isEditMode = ref(false);

const editId = ref(null)

async function getInstruments() {
  const { data } = await supabase.from('instruments').select()
  instruments.value = data
}

async function addInstrument(){
  if(!form.value?.name) return;
  if(isEditMode.value){
    const { error } = await supabase
    .from('instruments')
    .update({ name: form.value.name })
    .eq('id', editId.value)

    if(!error){
      form.value.name = ''
      isEditMode.value = false;
      editId.value = null;
      getInstruments()
    } else {
      console.log(error)
    }
  } else {
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
}

function editInstrument(item){
  form.value.name = item.name;
  isEditMode.value = true;
  editId.value = item.id
}
onMounted(() => {
  getInstruments()
})
</script>

<template>
  <div>
    <form @submit.prevent="addInstrument">
      <input type="text" v-model="form.name" placeholder="Enter instrument name">
      <button type="submit">
        {{ isEditMode ? 'Update' : 'Add' }}
      </button>
    </form>
  <ul>
    <li v-for="instrument in instruments" :key="instrument.id">{{ instrument.name }}
      <button @click="editInstrument(instrument)">Edit</button>
    </li>
    
  </ul>
    </div>
</template>