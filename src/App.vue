<script setup lang="ts">
import { ref, onMounted, computed, watch, Ref } from 'vue';

interface Todo {
    id: number
    content: string
    category: string
    completed: boolean
    createdAt: number
}

const todos: Ref<Todo[]> = ref([])
const name = ref("")

const input_content = ref("")
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt
}))


const addTodo = () => {
    if (input_content.value.trim() === "" || input_category.value === null) {
        alert('Please enter todo content and select a category.');
        return;
    }

    const newTodo = {
        id: Date.now(),
        content: input_content.value,
        category: input_category.value,
        completed: false,
        createdAt: Date.now()
    };

    todos.value.push(newTodo);

    // Reset input fields after adding the todo
    input_content.value = '';
    input_category.value = null;
    
}

const removeTodo = (id: number) => {
    todos.value = todos.value.filter((todo) => todo.id !== id)
}

watch(todos, newVal => {
    localStorage.setItem('todos', JSON.stringify(newVal))
}, {deep: true})

watch(name, (newVal) => {
    localStorage.setItem("name", newVal)
})

onMounted(() => {
    name.value = localStorage.getItem("name") || ""
    todos.value = JSON.parse(localStorage.getItem("todos") || "[]")
})


</script>

<template>
    <main class="app">
        <section class="greeting">
            <h2 class="title">
                Whats's up, <input type="text" placeholder="Name Here" v-model="name">
            </h2>
        </section>
        <section class="create-todo">
            <h3>CREATE TO-DO</h3>
            <form @submit.prevent="addTodo">
                <h4>What's on your to-do list</h4>
                <input type="text" placeholder="make a video" v-model="input_content">
                <h4>Pick a category</h4>

                <div class="options">
                    <label>
                        <input type="radio" name="category" value="business" v-model="input_category">
                        <span class="bubble business"></span>
                        <div>Business</div>
                    </label>
                    <label>
                        <input type="radio" name="category" value="personal" v-model="input_category">
                        <span class="bubble personal"></span>
                        <div>Personal</div>
                    </label>
                    
                </div>
                <input type="submit" value="Add To-do">
            </form>
        </section>
        <section class="todo-list">
            <h3>
                TODO LIST
            </h3>
            <div class="list">
                <div v-for="todo in todos_asc" :class="`todo-item ${todo.completed && 'done'}`">
                    <label>
                        <input type="checkbox" v-model="todo.completed">
                        <span :class="`bubble ${todo.category}`">
                        </span>
                    </label>

                    <div class="todo-content">
                        <input type="text" v-model="todo.content">
                    </div>

                    <div class="actions">
                        <button class="delete" @click="removeTodo(todo.id)">
                            
                        </button>
                    </div>

                </div>
            </div>
        </section>
    </main>
</template>

