<template>
    <div class="dp__action_row" :style="calendarWidth ? { width: `${calendarWidth}px` } : {}">
        <div class="dp__selection_preview">
            <slot name="action-preview" v-if="$slots['action-preview']" :value="internalModelValue" />
            <template v-if="!$slots['action-preview']">
                <template v-if="!Array.isArray(previewValue)">{{ previewValue }}</template>
                <template v-if="Array.isArray(previewValue)">
                    <div>{{ previewValue[0] }}</div>
                    <div>{{ previewValue[1] }}</div>
                </template>
            </template>
        </div>
        <div class="dp__action_buttons">
            <slot name="action-select" v-if="$slots['action-select']" :value="internalModelValue" />
            <template v-if="!$slots['action-select']">
                <span
                    v-if="!inline"
                    class="dp__action dp__cancel"
                    tabindex="0"
                    @click="$emit('closePicker')"
                    @keydown.enter="$emit('closePicker')"
                    >{{ cancelText }}</span
                >
                <span :class="selectClass" tabindex="0" @keydown.enter="selectDate" @click="selectDate">{{
                    selectText
                }}</span>
            </template>
        </div>
    </div>
</template>

<script lang="ts" setup>
    import { computed, ComputedRef, inject, PropType } from 'vue';
    import { Locale } from 'date-fns';
    import { IFormat, InternalModuleValue, ITimeValue } from '../interfaces';
    import { formatDate, getMonthVal, getTImeForExternal, isValidTime } from '../utils/date-utils';
    import { isModelValueRange } from '../utils/type-guard';

    const emit = defineEmits(['closePicker', 'selectDate']);

    const props = defineProps({
        selectText: { type: String as PropType<string>, default: 'Select' },
        cancelText: { type: String as PropType<string>, default: 'Cancel' },
        internalModelValue: { type: [Date, Array] as PropType<InternalModuleValue>, default: null },
        range: { type: Boolean as PropType<boolean>, default: false },
        previewFormat: {
            type: [String, Function] as PropType<IFormat>,
            default: () => '',
        },
        inline: { type: Boolean as PropType<boolean>, default: false },
        monthPicker: { type: Boolean as PropType<boolean>, default: false },
        timePicker: { type: Boolean as PropType<boolean>, default: false },
        twoCalendars: { type: Boolean as PropType<boolean>, default: false },
        calendarWidth: { type: Number as PropType<number>, default: 0 },
        menuMount: { type: Boolean as PropType<boolean>, default: false },
        customProps: { type: Object as PropType<Record<string, unknown>>, default: null },
        minTime: { type: Object as PropType<ITimeValue>, default: null },
        maxTime: { type: Object as PropType<ITimeValue>, default: null },
        enableTimePicker: { type: Boolean as PropType<boolean>, default: true },
    });
    const formatLocale = inject<ComputedRef<Locale>>('formatLocale');
    const selectClass = computed(() => ({
        dp__action: true,
        dp__select: true,
        dp__action_disabled: !isTimeValid.value,
    }));

    const isTimeValid = computed((): boolean => {
        if (!props.enableTimePicker) {
            return true;
        }
        return isValidTime(props.internalModelValue, props.maxTime, props.minTime);
    });

    const previewValue = computed((): string | string[] => {
        if (!props.internalModelValue || !props.menuMount) return '';
        if (typeof props.previewFormat === 'string') {
            if (isModelValueRange(props.internalModelValue)) {
                if (props.internalModelValue.length === 2 && props.internalModelValue[1]) {
                    if (props.twoCalendars) {
                        return `${formatDate(
                            props.internalModelValue[0],
                            props.previewFormat,
                            formatLocale?.value,
                        )} - ${formatDate(props.internalModelValue[1], props.previewFormat, formatLocale?.value)}`;
                    }
                    return [
                        formatDate(props.internalModelValue[0], props.previewFormat, formatLocale?.value),
                        formatDate(props.internalModelValue[1], props.previewFormat, formatLocale?.value),
                    ];
                }
                return `${formatDate(props.internalModelValue[0], props.previewFormat, formatLocale?.value)} -`;
            }
            return formatDate(props.internalModelValue, props.previewFormat, formatLocale?.value);
        }
        if (props.timePicker) {
            return props.previewFormat(getTImeForExternal(props.internalModelValue));
        }
        if (props.monthPicker) {
            return props.previewFormat(getMonthVal(props.internalModelValue as Date));
        }
        return props.previewFormat(props.internalModelValue);
    });

    const selectDate = (): void => {
        if (isTimeValid.value) {
            emit('selectDate');
        }
    };
</script>
