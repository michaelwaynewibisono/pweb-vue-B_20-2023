<template>
    <div class="navbar flex justify-between sticky top-0 z-999">
        <div class="flex my-auto text-2xl sm:text-3xl font-bold">To Do List App</div>
        <div class="icon shadow-lg rounded-full h-10 w-10 flex items-center justify-center" @click="showModal1">
            <i class="fa-solid fa-plus text-2xl"></i>
        </div>
    </div>
</template>

<script>
import './style.css';

export default {
    methods: {
        showModal1() {
            this.$emit('add-button');
        }
    }
}
</script>