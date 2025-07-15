<template>
	<VueDatePicker v-model="date" @update:model-value="$emit('date-picked', date)" />
</template>

<script setup>
import VueDatePicker from '@vuepic/vue-datepicker';
import '@vuepic/vue-datepicker/dist/main.css';
import { ref } from 'vue';

const date = ref();
</script>