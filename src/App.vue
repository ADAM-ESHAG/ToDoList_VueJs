<script setup>
  import { ref, onMounted, computed, watch } from 'vue';
  
  const todos = ref([])
  const nom = ref('')

  const input_content = ref('')
  const input_category = ref(null)

  const todos_asc = computed(() => todos.value.sort((a,b) => {
	return a.createdAt - b.createdAt
}))

watch(nom, (newVal) =>{

localStorage.setItem('nom', newVal)
}) 

  watch(todos, (newVal) => {
    localStorage.setItem('todos', JSON.stringify(newVal))
  }, {
    deep: true
  })


  const addTodo = () => {
    if(input_content.value.trim() === '' || input_category.value === null){
      return
    }

    todos.value.push({
      content: input_content.value,
      category: input_category.value,
      done: false,
      editable: false,
      createdAt: new Date().getTime()
    })

    input_content.value = ''
    input_category.value = null
  }

  const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}

  onMounted(() => {

    nom.value = localStorage.getItem('nom') || ''
    todos.value = JSON.parse(localStorage.getItem('todos')) || []
  })
</script>

<template>

  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Quoi de neuf, <input type="text" placeholder="nom ici" v-model="nom">
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREER UNE TACHE</h3>

      <form @submit.prevent="addTodo">
        <h4>Qu'y a-t-il sur votre liste de tâches ?</h4>
        <input 
                type="text"
                nom="content" 
					      id="content" 
                placeholder="e.g. faire une vidéo"
                v-model= "input_content"
        />

        <h4>Choisissez une catégorie</h4>
        <div class="options">
          <label>
            <input 
                  type="radio"
                  nom="category"
                  value="business"
                  v-model="input_category"
                  />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <label>
            <input 
                  type="radio"
                  nom="category"
                  value="personal"
                  v-model="input_category"
                  />
            <span class="bubble personal"></span>
            <div>Personnel</div>
          </label>
        </div>

        <input type="submit" value="Add todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>LISTE DE CHOSES A FAIRE</h3>
      <div class="list">
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
