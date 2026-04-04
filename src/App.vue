<script setup>
import { ref , computed , watch} from 'vue'

const todos = ref(JSON.parse(localStorage.getItem('todos')) || [
  { id: 1, title: '吃飯', isDone: false },
  { id: 2, title: '喝水', isDone: false },
  { id: 3, title: '睡覺', isDone: false }
])

const addTodoInput = ref('')

const addTodoFn = () => {
  if (addTodoInput.value.trim() === '') {
    alert('請輸入代辦事項')
    return
  }
  const newTodo = {
    id: Date.now(),
    title: addTodoInput.value,
    isDone: false
  }
  
  todos.value.push(newTodo)
  addTodoInput.value = ''  
}

// ✅ 正確：接收 id，用 filter 移除對應項目
const delTodoFn = (id) => {
  todos.value = todos.value.filter(todo => todo.id !== id)
}

const toggleTodo = (id) => {
  const target = todos.value.find(todo => todo.id === id)
  if (!target) return  // 防禦性寫法，找不到就離開
  target.isDone = !target.isDone
}

const finishedCount = computed(() => 
  todos.value.filter(todo => todo.isDone).length
)

const unfinishedCount = computed(() => 
  todos.value.filter(todo => !todo.isDone).length
)

watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, {deep:true})

</script>

<template>
  <div class="container">
    <ul>
     <li v-for="todo in todos" :key="todo.id">
      <span :class=" { done: todo.isDone} "> {{ todo.title }} </span> 
      <button @click="toggleTodo(todo.id)">
        {{ todo.isDone ? "點擊取消" : "點擊完成" }}
      </button>
      <button @click="delTodoFn(todo.id)">刪除</button>
     </li>
    </ul>
    <div >
      <input type="text" v-model="addTodoInput">
      <button @click="addTodoFn">新增</button>
       <p>已完成數量：{{ finishedCount }}</p>
        <p>未完成數量：{{ unfinishedCount }}</p>
    </div>
  </div>
</template>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 40px;
  font-family: sans-serif;
}

button {
  padding: 8px 24px;
  font-size: 16px;
  cursor: pointer;
}
.done {
  text-decoration: line-through;
  color: #aaa;
}
</style>