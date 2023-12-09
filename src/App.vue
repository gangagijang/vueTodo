<template>
	<main class="app">
		<section class="greeting">
			<h1 class="title">
				What's Up! 
				<input type="text" placeholder="이름 입력" v-model.trim.lazy="name">
			</h1>
		</section>

		<section class="create_todo">
			<h2>CREATE A TODO</h2>
			<form id="new-todo-form" v-on:submit.prevent="addTodo">
				<h4>What's on your todo list?</h4>
				<input class="todo_input" placeholder="할일을 입력해 주세요." v-model="input_content" >
				<div class="options">
					<label class="label">
						<input type="radio" name="category" id="category1" value="business" v-model="input_category">
						<span class="bubble business"></span>
						<div>Business</div>
					</label>

					<label class="label">
						<input type="radio" name="category" id="category2" value="personal" v-model="input_category">
						<span class="bubble personal"></span>
						<div>Personal</div>
					</label>
				</div>

				<input type="submit" value="Add Todo">

			</form>
		</section>

		<section class="todo_list">
			<h3>TODO LIST</h3>
			<div class="list">
				<div :class="`todo_item ${todo.done && 'done'}`" v-for="todo in todos_asc" :key="todo.createAt">
					<label >
						<input type="checkbox" v-model="todo.done">
						<span :class="`bubble ${todo.category}`"></span>
					</label>
					<div class="todo_content">
						<input type="text" v-model="todo.content">
					</div>
					<div class="actions">
						<button class="delete" @click="removeTodo(todo)">Delete</button>
					</div>
				</div>
			</div>
		</section>

	</main>
</template>

<script setup>
import { ref, watch, onMounted, computed } from 'vue';

	const name = ref('');
	const input_content = ref(null);
	const input_category = ref(null);
	const todos = ref([]);

	//삭제
	const removeTodo = (item) => {
		todos.value = todos.value.filter((aa)=>aa !== item)
	}

	//시간순으로 정리, 최근내역이 맨 위로 오게
	const todos_asc = computed(()=>todos.value.sort((a,b)=>b.createAt-a.createAt))

	//할일 입력값들을 받아오는 함수
	const addTodo = ()=> {

		if(input_content.value.trim() === '' || input_category.value === null) {
			return;
		}

		//console.log('할일 입력값 받아오는 함수 실행');
		todos.value.push({
			content:input_content.value,
			category:input_category.value,
			done:false,
			createAt:new Date().getTime()	//시간순으로 순서를 넣기 위해 UTC1970년 1월1일~현재까지 몇초(ms, 밀리세컨) 지났는지 알아옴.
		})

		//입력후 초기화
		input_content.value = '';
		input_category.value = null;
	}

	//todos가 바뀌는 것 감지
	watch(todos, (newVal)=>{
		//console.log('todos 감지',newVal);
		localStorage.setItem('todos',JSON.stringify(newVal));
	},{deep:true})	//deep - 속성의 프로퍼티나 하위 뎁스도 감지

	//이름 입력을 감지하고 로컬 스토리지에 저장
	watch(name,(newName)=>{
		localStorage.setItem('name', newName)
  })

	//새로 열었을때 로컬스토리지에서 불러옴 (없을때는 비워놓음)
	onMounted(()=>{
    name.value = localStorage.getItem('name') || '';
    todos.value = JSON.parse(localStorage.getItem('todos') || []);
		console.log('todos 새로 불러옴',todos.value);
  })
</script>
