<script lang="ts">

export default {
  inheritAttrs: false
}
</script>

<script setup lang="ts">
import { computed, ref } from 'vue';

const props = defineProps<{
  list: any[],
  modelValue: any,
  itemLabel: string
}>();
// const emit = defineEmits<{
//   'update:modelValue': any
// }>();
const emit = defineEmits(['update:modelValue', 'search']);

const label = ref('');
const picked = ref(false);
const current = ref(-1);

const filteredList = computed(() => {
  if (picked.value) return [];
  if (label.value.length == 0) return [];
  return props.list.filter(v => v[props.itemLabel].match(new RegExp(label.value, 'i')));
});

function makeBold(text: string): string {
  return text.replace(new RegExp(`(${label.value})`, 'i'), `<strong>$1</strong>`);
}

function enter() {
  if (current.value == -1) return;
  selectOption(current.value);
}
function down() {
  if (filteredList.value.length == 0) return;
  current.value += 1;
  if (current.value > filteredList.value.length - 1) {
    current.value = filteredList.value.length - 1;
  }
}
function up() {
  if (filteredList.value.length == 0) return;
  current.value -= 1;
  if (current.value < 0) {
    current.value = 0;
  }
}
function press(e: any) {
  if (e.code != 'Enter') {
    emit('search', label.value);
    picked.value = false;
  }
}

function selectOption(i: number) {
  const item = filteredList.value[i];
  label.value = filteredList.value[i][props.itemLabel];
  picked.value = true;
  current.value = -1;
  emit('update:modelValue', item);
}

function done() {
  if ( ! picked.value) {
    emit('update:modelValue', label.value);
  }
}

</script>

<template>
  <div class="autocomplete-wrapper">
    <input type="text" :class="$attrs.class" @keyup.passive="press" @keydown.enter="enter" @keydown.down="down" @keydown.up="up" v-model="label" @change="done">
    <ul class="dropdown-menu" :class="{ show: filteredList.length > 0 }">
      <li v-for="(l, i) in filteredList" :key="i"><a class="dropdown-item" :class="{ active: i == current }" href="" @click.prevent="selectOption(i)" v-html="makeBold(l[props.itemLabel])"></a></li>
    </ul>
  </div>
  
</template>

<style lang="css">
.autocomplete-wrapper {
  position: relative;
}

.dropdown-menu {
  width: 100%;
}
</style>

