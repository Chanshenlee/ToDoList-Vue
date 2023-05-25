<script setup>

import {ref , onMounted , computed , watch } from 'vue'

// ref 是Vue中的一個函式，它用於創建一個可響應的數據引用。通常用於定義組件中的數據。
// onMounted 它會在Vue組件被掛載到 DOM 後執行。在這個函式內部，你可以執行初始化操作、註冊事件監聽器等。
// watch 是 Vue 中的一個函式，用於監聽數據的變化。通過設定監聽器，你可以在數據發生變化時執行相應的回調函式，進行相關的處理。
const todos = ref([]) 
const name = ref('')

const input_content = ref('') // 輸入內容
const input_category = ref(null) // 輸入類別

const todos_asc = computed(() => todos.value.sort((a,b) =>{
	return b.createdAt - a.createdAt
}))
// 對 todos.value 進行排序 
// 使用了物件的 createdAt 屬性值的差異來進行排序，以確保 todos_asc 是根據 createdAt 屬性的升序排列。
// return b.createdAt - a.createdAt 的結果是先進先出 後新增的會在最上面。

watch(name, (newVal) => {
	localStorage.setItem('name', newVal)
  console.log(newVal);
})
// 回調函式的作用是將 newVal 的值存儲在 localStorage 中的名為 name 的項目中。
// 這樣做可以在名為 name 的數據變化時將數據保存在瀏覽器的本地存儲中。

watch(todos, function(newVal)  {
	localStorage.setItem('todos', JSON.stringify(newVal))
  console.log(newVal);
  // 一樣是把 newVal 的值轉成 JSON 格式儲存在 LocalStorage 中的名為 todo 的項目中。
  // 這樣做可以在名為 todo 的數據變化時將數據保存在瀏覽器的本地存儲中。
}, {
	deep: true 
  //它指定了在監聽 todos 變化時進行深度監聽。這意味著如果 todos 中的任何子層屬性發生變化，也會觸發。
})

const addTodo = () => {
	if (input_content.value.trim() === '' || input_category.value === null) {
		return 
	}

	todos.value.push({
		content: input_content.value,
		category: input_category.value,
		done: false,  // 一個布林值，表示該項目是否已完成，這裡預設為 false
		editable: false, // 表示該項目是否可編輯
		createdAt: new Date().getTime()
	})
}

const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}

onMounted(() => {
	name.value = localStorage.getItem('name') || '' // 將前面儲存在本地端的值給 name.value，如果在本地儲存端沒有值則傳空值
	todos.value = JSON.parse(localStorage.getItem('todos')) || []
})

</script>

<template>

  <div class="app">

    <section class="greeting">

      <h2 class="title">
        Welcome User , <input type="text" id="name" placeholder="Name here" v-model="name"/>
      </h2>

    </section>

    <section class="create-todo">

      <h3>創建一個代辦事項</h3>
      <form id="new-todo-form" @submit.prevent="addTodo">
        
        <input type="text" name="content" id="content" placeholder="請輸入代辦事項" v-model="input_content">
        
        <h4>選擇類型</h4>

        <div class="options">

          <label>
            <input type="radio" name="category" value="business" v-model="input_category">
            <span class="bubble business"></span>
            <div>Work</div>
          </label>

          <label>
            <input type="radio" name="category" value="personal" v-model="input_category">
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>

        </div>

        <input type="submit" value="Add todo">
      </form>

    </section>

    <section class="todo-list">

        <h3>TODO LIST</h3>

        <div class="list" id="todo-list">
          <div v-for="todo in todos_asc" :key="todo.createdAt" :class="`todo-item ${todo.done && 'done'}`">

            <label>
              <input type="checkbox" v-model="todo.done" />
              <span :class="`bubble ${todo.category == 'business' ? 'business' : 'personal'}`"></span>
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

  </div>



</template>


