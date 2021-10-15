<script setup lang="ts">
import { ref } from 'vue';
import Autocomplete from './components/Autocomplete.vue'
  
let options = ref([]);

const value = ref<{ nama:string}>({
  nama:''
});

const getList = async (e: any) => {
  const r = await fetch(`http://localhost:8181/pembeli?nama=${e}`);
  const d = await r.json();
  options.value = d.map((v: any) => {
    return {nama:v.nama}
  });
  console.log(d)
  console.log(options.value)
}
</script>

<template>
  <div class="container">
    <div class="row">
      <div class="col m-3">
        
        <div class="mb-3 row">
          <label for="staticEmail" class="col-sm-2 col-form-label">Email</label>
          <div class="col-sm-4">
            <Autocomplete :list="options" item-label="nama" v-model="value" class="form-control" @search="getList"></Autocomplete>
            <hr>
            Nilai Model: {{ value }}
          </div>
        </div>

      </div>
    </div>
  </div>
</template>