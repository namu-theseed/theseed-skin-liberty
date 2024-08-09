<template>
    <div ref="dropdown" class="dropdown" :class="{ 'open': show }">
        <div @click="toggle"><slot name="toggle"></slot></div>
        <slot v-if="show"></slot>
        <div v-if="show" class="backdrop" @click="hide"></div>
    </div>
</template>

<style scoped>
.backdrop {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    z-index: 999;
}
</style>

<script>
export default {
    data() {
        return {
            show: false,
        }
    },
    methods: {
        toggle() {
            this.show = !this.show;
        },
        hide() {
            this.show = false;
        }
    }
}
</script>