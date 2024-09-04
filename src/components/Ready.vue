<template>
  {{ a }}
</template>


<script setup name="Ready">
const a = ref('1')
console.log(a.value)
</script>