<template>
	<div class="container">
		<h1>Task Management</h1>
		<task-form @task-added="addTask"></task-form>
		<div id="tasks">
			<table id="taskTable" class="table">
				<thead class="thead-light">
					<tr>
						<th scope="col">Title</th>
						<th scope="col">Description</th>
						<th scope="col">Status</th>
						<th scope="col">Due Date</th>
						<th></th>
					</tr>
				</thead>
				<tbody>
					<tr v-for="task in tasks" :key="task.id">
						<td>{{ task.title }}</td>
						<td>{{ task.description }}</td>
						<td>
							<StatusList 
								id="status-input" 
								:taskId=task.id 
								:statusId=task.status 
								:statuses="statuses"/>
						</td>
						<td>{{ task.due_date }}</td>
						<td>
							<a href="javascript:;" 
									v-on:click="deleteTask(task.id, task.title)">Delete</a>
						</td>
					</tr>
				</tbody>
			</table>
		</div>
		<nav aria-label="Page navigation">
			<ul class="pagination justify-content-center">
				<li v-if="links.previous !== null" class="page-item">
					<a class="page-link" v-on:click="navigation(links.previous)">Previous page</a>
				</li>
				<li v-if="links.next !== null" class="page-item">
					<a class="page-link" v-on:click="navigation(links.next)">Next page</a>
				</li>
			</ul>
		</nav>
	</div>
</template>

<script>
import TaskForm from "./components/TaskForm.vue";
import StatusList from "./components/StatusList.vue";
import axios from 'axios';

export default {
	name: 'App',
	components: {
		TaskForm,
		StatusList
	},
	data() {
		return {
			tasks: [],
			links: [],
			statuses: [],
		};
	},
	mounted() {
		axios.all([
			axios.get(
					"http://127.0.0.1:8000/api/task_list/", 
					{withCredentials: false,}
			),
			axios.get(
					"http://127.0.0.1:8000/api/statuses/", 
					{withCredentials: false,}
			)
		])
		.then(axios.spread((data1, data2) => {
			this.tasks = data1.data.tasks,
			this.links = data1.data.links,
			this.statuses = data2.data
		}))
		.catch(error => 
			console.log(error)
		);
	},
	methods: {
		// called after a task is added to refresh the task list
		addTask() {
			window.location.reload()
		},

		async navigation(link) {
			const { data } = await this.axios.get(link,{withCredentials: false,});
			this.tasks = data.tasks;
			this.links = data.links;
		},

		deleteTask(id, title) {
			if(confirm("Do you really want to delete the task with title ''" + title + "' ?")){
				this.axios.delete('http://127.0.0.1:8000/api/task_list/'+id+'/')
				.then(
					window.location.reload()
				)
				.catch(error => {
					console.log(error);
				});
			}
		},
	}
}
</script>

<style>
#app {
	font-family: Avenir, Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	color: #2c3e50;
	margin-top: 60px;
	max-width:300vh;
	display: flex;
	justify-content: center;
	align-items: center;
	text-align: center;
}

#status-input {
	width: 100px;
}

h1 {
	padding-bottom: 40px;
}

table {
	margin-top: 20px;
}
</style>
