<template>
    <div ref="el" class="scrollama__steps">
        <slot />
    </div>
</template>

<script setup>
import scrollama from 'scrollama';
import { computed, ref, onBeforeUnmount, onMounted, useAttrs } from 'vue';

let scroller = scrollama();
const attrs = useAttrs()
const emit = defineEmits(['step-progress', 'step-enter', 'step-exit'])
const el = ref(null)
const opts = computed(() => {
    let rootElem = el.value
    return Object.assign({}, {
        step: Array.from(rootElem.children),
        progress: !!attrs['step-progress']
    }, attrs)
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