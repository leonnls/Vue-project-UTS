<script setup>
import { ref, onMounted, computed, watch } from 'vue'
                                                                                                                                                              
const todos = ref([])
const name = ref('')
                                                                                                                                                                   
const input_content = ref('')
const input_category = ref(null)
const editingTodo = ref(null);

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


// const addTodo = () => {
// 	if (input_content.value.trim() === '' || input_category.value === null) {
// 		return
// 	}

// 	todos.value.push({
// 		content: input_content.value,
// 		category: input_category.value,
// 		done: false,
// 		editable: false,
// 		createdAt: new Date().getTime()
// 	})
// }
                      
const addTodo = () => {
   if (input_content.value.trim() === '' || input_category.value === null) {
      return;
   }

   if (editingTodo.value) {
      // Jika sedang menyunting todo
      editingTodo.value.content = input_content.value;
      editingTodo.value.category = input_category.value;
      editingTodo.value.editable = false;
      editingTodo.value = null; // Reset todo yang sedang diedit
   } else {
      // Jika menambahkan todo baru
      todos.value.push({
         content: input_content.value,
         category: input_category.value,
         done: false,
         editable: false,
         createdAt: new Date().getTime()
      });
   }

   // Reset input setelah menambah atau menyunting todo
   input_content.value = '';
   input_category.value = null;
};
                                                                                                                       
const startEditing = (todo) => {
   editingTodo.value = todo;
   input_content.value = todo.content;
   input_category.value = todo.category;
};

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
			<h0 class="title">
				Halo, <input type="text" id="name" placeholder="Name here" v-model="name">
			</h0>
      <h3>Selamat Datang</h3>
		</section>

		<section class="create-todo">
			<h2>Buat Kegiatan! </h2>

			<form id="new-todo-form" @submit.prevent="addTodo">
				<input 
					type="text" 
					name="content" 
					id="content" 
					placeholder="Contoh : Cuci Baju"
					v-model="input_content" />
				
				<h2>Jenis Hal Kegiatan</h2>
				<div class="options">

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category1" 
							value="business"
							v-model="input_category" />
						<span class="bubble business"></span>
						<div>Perkuliahan</div>
					</label>
                              
					<label>
						<input 
							type="radio" 
							name="category" 
							id="category2" 
							value="personal"
							v-model="input_category" />
						<span class="bubble personal"></span>
						<div>Personal</div>
					</label>

				</div>

				<input type="submit" value="Add todo" />
				<input type="submit" :value="editingTodo ? 'Simpan Perubahan' : 'Edit Todo'" style="margin-top: 10px; background-color: royalblue;"/>
			</form>
		</section>

		<section class="todo-list">
			<h3>TODO LIST</h3>
			<div class="list" id="todo-list">
				
				<div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${
							todo.category == 'business' 
								? 'business' 
								: 'personal'
						}`"></span>
					</label>

					<div class="todo-content">
						<input type="text" v-model="todo.content" :disabled="todo.editable" />
					</div>

					<div class="actions">
						<button class="delete" @click="removeTodo(todo)">Hapus</button>
					</div>

					<div class="actions">
						<button class="edit" @click="startEditing(todo)" style="margin-left: 10px; background-color: royalblue;">Edit</button>
					</div>
				</div>

			</div>
		</section>

	</main>
</template>

