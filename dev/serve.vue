<template>
    <div class="wrapper">
        <vue3-date-picker v-model="selectedDate" range twoCalendars :presetRanges="presetRanges" />
    </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue';
import { subDays, endOfMonth, startOfMonth, subMonths } from 'date-fns'
import Vue3DatePicker from '@/Vue3DatePicker/Vue3DatePicker.vue';

const selectedDate = ref();
const presetRanges = ref([
    {
        label: 'Today',
        ranges: [new Date(), new Date()],
    },
    {
        label: 'Yesterday',
        ranges: [subDays(new Date, 1), subDays(new Date, 1)],
    },
    {
        label: 'Last 7 Days',
        ranges: [subDays(new Date, 6), new Date()],
    },
    {
        label: 'Last 30 Days',
        ranges: [subDays(new Date, 29), new Date()],
    },
    {
        label: 'This Month',
        ranges: [startOfMonth(new Date), endOfMonth(new Date())],
    },
    {
        label: 'Last Month',
        ranges: [startOfMonth(subMonths(new Date, 1)), endOfMonth(subMonths(new Date(), 1))],
    },
]);
</script>

<style lang="scss">
@import 'src/Vue3DatePicker/style/main';
.wrapper {
    padding: 200px;
}
</style>
