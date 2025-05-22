<template><Icon v-if="name" :icon="name" /></template>
<script setup>
import { Icon } from "@iconify/vue";

const props = defineProps({
  name: String,
});
</script>
