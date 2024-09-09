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
	<h1>My Day</h1>
<input onkeydown={addTodo} placeholder="Add todo" type="text" />

	<div class="todos">
		{#each filteredTodos as todo, i}
			<div class:completed={todo.done} class="todo">
				<input oninput={editTodo} data-index={i} value={todo.text} type="text" />
				<div class="todo-actions">
					<input onchange={toggleTodo} data-index={i} checked={todo.done} type="checkbox" />
					<button onclick={() => deleteTodo(i)} class="delete-btn">❌</button>
				</div>
			</div>
		{/each}
	</div>

</div>

<div class="filters">
	<button onclick={() => setFilter('all')}>All</button>
	<button onclick={() => setFilter('active')}>Active</button>
	<button onclick={() => setFilter('completed')}>Completed</button>
</div>

<p class="remaining">{remaining()} remaining</p>

<style>
    .todo-app-container {
        max-width: 700px;
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
        padding-right: 110px; /* Increased to accommodate checkbox and delete button */
        min-width: 0; /* Allows the input to shrink below its default size */
    }

    .todo-actions {
        position: absolute;
        right: 10px;
        top: 50%;
        transform: translateY(-50%);
        display: flex;
        align-items: center;
        gap: 5px; /* Add some space between checkbox and delete button */
    }

    input[type='checkbox'] {
        margin: 0; /* Remove default margins */
    }

    .delete-btn {
        background-color: wheat;
				border: red solid 1px;
        color: white;
        padding: 0.2rem;
        cursor: pointer;
        border-radius: 4px;
        white-space: nowrap; /* Prevent "Delete" from wrapping */
    }

    .delete-btn:hover {
        background-color: lightpink;
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
				font-size: 1.5rem;
        color: #0A2342;
        text-align: center;
    }
		h1 {
        color: #0A2342;
        text-align: center;
		}
</style>