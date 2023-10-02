<template>
    <child-component @add-button="showModal1 = true" />

    <div class="bg" :class="{ 'show2': showModal2 }">
        <div class="submit rounded-xl">
            <button class="icon shadow-lg rounded-md h-10 w-10 items-center justify-center" @click="closeEdit">
                <i class="fa-solid fa-x text-2xl"></i>
            </button><br><br>
            <input type="text" v-model="taskTitle" class="submit-title" placeholder="Title"><br>
            <textarea rows="6" v-model="taskText" class="submit-text" placeholder="input notes"></textarea><br><br>
            <button class="bg-blue-500 hover:bg-blue-700 text-white font-semibold py-2 px-4 rounded"
                @click="updateTask">Update</button>
        </div>
    </div>

    <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-5">
        <div class="cards rounded-lg shadow-2xl my-2 btn" v-for="(task, index) in tasks" :key="index" :style="{
            backgroundColor:
                task.activeButton === 'daily' ? '#ff8e47' :
                    task.activeButton === 'urgent' ? '#ff6262' :
                        task.activeButton === 'important' ? '#B388F9' : '#F5F5F5'
        }" :class="{ 'show3': task.completed }">
            <div class="flex justify-between">
                <div></div>
                <input type="checkbox" class="checkbox" :checked="task.completed" @click="checkList(index)"
                    :disabled="task.completed">
            </div>
            <div class="title text-4xl font-bold">{{ task.title }}</div>
            <div class="text my-5">{{ task.text }}</div>
            <div class="acc flex justify-between">
                <div id="time">{{ task.time }}</div>
                <div class="buttons flex gap-4">
                    <div class="edit" @click="showModal2 = true" v-if="!task.completed">
                        <i class="fa-solid fa-pen-to-square text-2xl" @click="readTask(index)"></i>
                    </div>
                    <div class="delete" @click="deleteTask(index)">
                        <i class="fa-solid fa-trash text-2xl"></i>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <div class="bg" :class="{ 'show1': showModal1 }">
        <div class="submit rounded-xl">
            <button class="icon shadow-lg rounded-md h-10 w-10 items-center justify-center" @click="showModal1 = false">
                <i class="fa-solid fa-x text-2xl"></i>
            </button><br><br>
            <input type="text" class="submit-title" v-model="taskNewTitle" placeholder="Title"><br>
            <textarea rows="6" class="submit-text" v-model="taskNewText" placeholder="input notes"></textarea><br><br>
            <div class="flex justify-between">
                <div class="flex gap-3">
                    <div class="rounded-full border-solid border flex px-4 py-2 border-black"
                        @click="toggleCategory('daily')" :class="{ 'orange': activeButton === 'daily' }" id="btn">daily
                    </div>
                    <div class="rounded-full border-solid border flex px-4 py-2 border-black"
                        @click="toggleCategory('urgent')" :class="{ 'red': activeButton === 'urgent' }" id="btn">urgent
                    </div>
                    <div class="rounded-full border-solid border flex px-4 py-2 border-black"
                        @click="toggleCategory('important')" :class="{ 'purple': activeButton === 'important' }" id="btn">
                        important
                    </div>
                </div>
                <button class="bg-blue-500 hover:bg-blue-700 text-white font-semibold py-2 px-4 rounded"
                    @click="createTask">Submit</button>
            </div>
        </div>
    </div>
</template>

<script>
import { format } from 'date-fns';
import ChildComponent from './NavbarComp.vue';
import './style.css';

export default {
    components: {
        ChildComponent,
    },

    data() {
        return {
            showModal1: false,
            showModal2: false,
            taskNewTitle: '',
            taskNewText: '',
            taskTitle: '',
            taskText: '',
            activeButton: 'default',
            tasks: [
                {
                    title: 'Tugas SISOP',
                    text: 'Lorem ipsum dolor sit amet consectetur, adipisicing elit. Totam, doloremque odit! Necessitatibus dolorum pariatur perspiciatis porro, impedit similique id quos error molestias dolore quidem ipsam excepturi fuga minima magni nam.',
                    time: '12.00',
                },
                {
                    title: 'TKA',
                    text: 'Lorem ipsum dolor sit amet consectetur, adipisicing elit. Nisi, deleniti quisquam deserunt, ipsa vitae voluptatum laborum voluptas fuga explicabo pariatur consequuntur at perspiciatis minus, inventore incidunt nostrum quidem eum sapiente!',
                    time: '12.15',
                },
            ],
        }
    },

    created() {
        const storedTasks = localStorage.getItem('tasks');
        if (storedTasks) {
            this.tasks = JSON.parse(storedTasks);
        }
    },

    methods: {
        createTask() {
            let timestamp = format(new Date(), "HH:mm");
            if (this.taskNewTitle.length == 0 || this.taskNewText.length == 0) {
                return;
            } else {
                this.tasks.push({
                    title: this.taskNewTitle,
                    text: this.taskNewText,
                    time: timestamp,
                    activeButton: this.activeButton,
                });

                localStorage.setItem('tasks', JSON.stringify(this.tasks));
            }

            this.taskNewTitle = '';
            this.taskNewText = '';
            this.activeButton = 'default';
        },

        toggleCategory(button) {
            this.activeButton = button;
        },

        readTask(index) {
            this.temp = index,
                this.taskTitle = this.tasks[this.temp].title;
            this.taskText = this.tasks[this.temp].text;
            if (!this.showModal2) this.$router.push({ path: '/' });
            this.$router.push({ path: `/edit/${this.temp}` });
        },

        updateTask() {
            if (this.temp !== null) {
                this.tasks[this.temp].title = this.taskTitle;
                this.tasks[this.temp].text = this.taskText;
                this.temp = null; // Reset temp
                this.showModal2 = false; // Hide the edit modal after updating
                this.$router.push({ path: '/todolist' });
            }

            localStorage.setItem('tasks', JSON.stringify(this.tasks));
        },

        closeEdit() {
            this.showModal2 = false;
            this.$router.push({ path: '/todolist' });
        },

        deleteTask(index) {
            this.tasks.splice(index, 1);
            localStorage.setItem('tasks', JSON.stringify(this.tasks));
        },

        checkList(index) {
            this.tasks[index].completed = true;
            localStorage.setItem('tasks', JSON.stringify(this.tasks));
        }
    },
}
</script>