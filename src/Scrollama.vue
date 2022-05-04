<template>
    <div ref="el" class="scrollama__steps">
        <slot />
    </div>
</template>

<script setup lang="ts">
import scrollama, { ScrollamaInstance, ScrollamaOptions } from 'scrollama';
import { computed, ref, Ref, onBeforeUnmount, onMounted, defineEmits, useAttrs } from 'vue';

let scroller:ScrollamaInstance = scrollama();
const attrs = useAttrs()
const emit = defineEmits(['step-progress', 'step-enter', 'step-exit'])
const el: Ref<HTMLDivElement | null> = ref(null)
const opts = computed(() => {
    let rootElem = el.value as HTMLDivElement
    return Object.assign({}, {
        step: Array.from(rootElem.children),
        progress: !!attrs['step-progress']
    }, attrs) as ScrollamaOptions
})

onMounted(() => {
    scroller.destroy()
    scroller
        .setup(opts.value)
        .onStepProgress(resp => {
          emit('step-progress', resp)
        })
        .onStepEnter(resp => {
          emit('step-enter', resp);
        })
        .onStepExit(resp => {
          emit('step-exit', resp);
        })

    window.addEventListener('resize', handleResize);
})

onBeforeUnmount(() => {
    scroller.destroy();
})

function handleResize(){
    scroller.resize()
}


</script>