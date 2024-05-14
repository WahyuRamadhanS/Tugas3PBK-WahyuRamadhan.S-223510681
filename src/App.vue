<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')

const filter = ref('all')


const todos_asc = computed(() => todos.value.sort((a,b) =>{
	return a.createdAt - b.createdAt
}))

watch(name, (newVal) => {
	localStorage.setItem('name', newVal)
})

watch(todos, (newVal) => {
	localStorage.setItem('todos', JSON.stringify(newVal))
}, {
	deep: true
})

const filteredTodos = computed(() => {
    switch (filter.value) {
        case 'complete':
            return todos.value.filter(todo => todo.done);
        case 'notComplete':
            return todos.value.filter(todo => !todo.done);
        default:
            return todos.value;
    }
});

const addTodo = () => {
	if (input_content.value.trim() === '') {
		return
	}

	todos.value.push({
		content: input_content.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime()
	})
}

const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}

onMounted(() => {
	name.value = localStorage.getItem('name') || ''
	todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
    <main class="app">
        <section class="greeting">
            <h2 class="title">
                Hai welcome to, <input type="text" id="name" placeholder="Name here" v-model="name"> Note
            </h2>
        </section>

        <section class="create-todo">
            <h3>CREATE ACTIVITIES</h3>
            <form id="new-todo-form" @submit.prevent="addTodo">
                <h4>What's on your activities?</h4>
                <input type="text" id="content" placeholder="Add your activities" v-model="input_content" />
                <input type="submit" value="Add todo" />
            </form>
        </section>
    </main>

    <main class="app2">
        <section class="todo-list">
            <h3>Your Activities</h3>
            <div>
                <!-- Filter Buttons -->
                <button @click="filter = 'all'"> All </button>
				<button @click="filter = 'complete'"> Complete </button>
                <button @click="filter = 'notComplete'"> Not Complete </button>
                
            </div>
            <div class="list" id="todo-list">
                <div v-for="todo in filteredTodos" :key="todo.createdAt" :class="`todo-item ${todo.done ? 'done' : ''}`">
                    <label>
                        <input type="checkbox" v-model="todo.done" />
                        <span class="bubble"></span>
                    </label>
                    <div class="todo-content">
                        <input type="text" v-model="todo.content" />
                    </div>
                    <div class="actions">
                        <button class="delete" @click="removeTodo(todo)">Delete</button>
                    </div>
                </div>
            </div>
        </section>
    </main>
</template>
