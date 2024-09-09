<script lang="ts">
	type Todo = {
		text: string
		done: boolean
	}
	type Filters = 'all' | 'active' | 'completed'

	let todos = $state<Todo[]>([])
	let filter = $state<Filters>('all')
	let filteredTodos = $derived(filterTodos())

	$effect(() => {
		const savedTodos = localStorage.getItem('todos')
		savedTodos && (todos = JSON.parse(savedTodos))
	})

	$effect(() => {
		localStorage.setItem('todos', JSON.stringify(todos))
	})

	function addTodo(event: KeyboardEvent) {
		if (event.key !== 'Enter') return

		const todoEl = event.target as HTMLInputElement
		const text = todoEl.value
		const done = false

		todos = [...todos, { text, done }]

		todoEl.value = ''
	}

	function editTodo(event: Event) {
		const inputEl = event.target as HTMLInputElement
		const index = +inputEl.dataset.index!
		todos[index].text = inputEl.value
	}

	function toggleTodo(event: Event) {
		const inputEl = event.target as HTMLInputElement
		const index = +inputEl.dataset.index!
		todos[index].done = !todos[index].done
	}

	function setFilter(newFilter: Filters) {
		filter = newFilter
	}

	function filterTodos() {
		switch (filter) {
			case 'all':
				return todos
			case 'active':
				return todos.filter((todo) => !todo.done)
			case 'completed':
				return todos.filter((todo) => todo.done)
		}
	}

	function remaining() {
		return todos.filter((todo) => !todo.done).length
	}

	function deleteTodo(index: number) {
		todos = todos.filter((_, i) => i !== index);
	}

</script>
<div class="todo-app-container">
<input onkeydown={addTodo} placeholder="Add todo" type="text" />

<div class="todos">
	{#each filteredTodos as todo, i}
		<div class:completed={todo.done} class="todo">
			<input oninput={editTodo} data-index={i} value={todo.text} type="text" />
			<input onchange={toggleTodo} data-index={i} checked={todo.done} type="checkbox" />
			<button onclick={() => deleteTodo(i)} class="delete-btn">X</button>
		</div>
	{/each}
</div>
</div>

<!--<div class="todos">
	{#each filteredTodos as todo, i}
		<div class:completed={todo.done} class="todo">
			<input oninput={editTodo} data-index={i} value={todo.text} type="text" />
			<input onchange={toggleTodo} data-index={i} checked={todo.done} type="checkbox" />
		</div>
	{/each}
</div>-->

<div class="filters">
	<button onclick={() => setFilter('all')}>All</button>
	<button onclick={() => setFilter('active')}>Active</button>
	<button onclick={() => setFilter('completed')}>Completed</button>
</div>

<p class="remaining">{remaining()} remaining</p>

<style>
    .todo-app-container {
        max-width: 600px;
        margin: 0 auto;
        padding: 1rem;
    }

    .todos {
        display: grid;
        gap: 1rem;
        margin-block-start: 1rem;
    }

    .todo {
        position: relative;
        transition: opacity 0.3s;
        display: flex;
        align-items: center;
    }

    .completed {
        opacity: 0.5;
    }

    input[type='text'] {
        width: 100%;
        padding: 0.75rem;
        font-size: 1rem;
        border: 1px solid #ccc;
        border-radius: 4px;
    }

    .todo input[type='text'] {
        flex-grow: 1;
        padding-right: 100px; /* Make room for checkbox and delete button */
        text-overflow: ellipsis;
    }

    .todo-actions {
        position: absolute;
        right: 10px;
        top: 50%;
        transform: translateY(-50%);
        display: flex;
        align-items: center;
    }

    input[type='checkbox'] {
        margin-right: 10px;
    }

    .delete-btn {
        background-color: #ff4136;
        color: white;
        border: none;
        padding: 0.5rem;
        cursor: pointer;
        border-radius: 4px;
    }

    .delete-btn:hover {
        background-color: #ff1a1a;
    }

    .filters {
        margin-block: 1rem;
        display: flex;
        justify-content: center;
        gap: 0.5rem;
    }

    .filters button {
        padding: 0.5rem 1rem;
        background-color: #0074D9;
        color: white;
        border: none;
        border-radius: 4px;
        cursor: pointer;
    }

    .filters button:hover {
        background-color: #0056b3;
    }

    .remaining {
        color: black;
        text-align: center;
    }
</style>

<!--
<style>
    .todos {
        display: grid;
        gap: 1rem;
        margin-block-start: 1rem;
    }

    .todo {
        position: relative;
        transition: opacity 0.3s;
    }

    .completed {
        opacity: 0.5;
    }

    input[type='text'] {
        width: 100%;
        padding: 1rem;
    }

    input[type='checkbox'] {
        position: absolute;
        right: 4%;
        top: 50%;
        translate: 0% -50%;
    }

    .filters {
        margin-block: 1rem;
    }
		.remaining {
				color: black;
		}
    .delete-btn {
        position: absolute;
        right: 1%;
        top: 50%;
        transform: translateY(-50%);
        background-color: #ff4136;
        color: white;
        border: none;
        padding: 0.5rem;
        cursor: pointer;
        border-radius: 4px;
    }

    .delete-btn:hover {
        background-color: #ff1a1a;
    }

    input[type='checkbox'] {
        right: 25%;  /* Adjust this to make room for the delete button */
    }

</style>
-->
