<template>
    <div class="dp__ranges_wrapper">
        <div
            v-for="(preset, index) in presetRanges"
            :key="index"
            class="dp__ranges_item"
            @click="applyRange(preset.ranges)"
            :class="isRangeMatch(selectedRanges, preset.ranges) ? 'dp__ranges_item-selected' : ''"
        >
            {{ preset.label }}
        </div>
    </div>
</template>

<script lang="ts" setup>
    import { PropType } from 'Vue';
    import { startOfDay, isEqual } from 'date-fns';

    const emit = defineEmits(['onSelect']);
    const props = defineProps({
        presetRanges: { type: Array as PropType<object[]>, default: () => [] },
        selectedRanges: { type: Array as PropType<object[]>, default: () => [] },
    });

    function applyRange(ranges) {
        emit('onSelect', ranges);
    }

    const isRangeMatch = (array1, array2) => {
        if (!array1 || !array2 || typeof array1 !== 'object' || typeof array2 !== 'object') return false;
        if (array1.length !== array2.length) return false;

        return array1.every((date, index) => {
            return isEqual(startOfDay(date), startOfDay(array2[index]));
        });
    };
</script>
