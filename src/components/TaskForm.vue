<template>
	<a class="btn btn-secondary btn-block" 
		data-toggle="collapse" href="#collapseAdd" 
		role="button" 
		aria-expanded="false" 
		aria-controls="collapseAdd">Add a task</a>
	<div class="collapse" id="collapseAdd">
		<div class="card card-body">
			<form @submit.prevent="onSubmit">
				<h3 class="label-wrapper">
					<label for="new-task-input" class="label__lg">What needs to be done?</label>
				</h3>
				<p v-if="errors.length">
					<b>Please correct the following error(s):</b>
					<ul class="list-group">
						<li v-for="error in errors" 
							v-bind:key="error" 
							class="list-group-item list-group-item-danger">{{ error }}</li>
					</ul>
				</p>
				<div class="form-group">
					<label for="title-input">Title</label>
					<input
						type="text"
						id="title-input"
						maxlength="32"
						autocomplete="off"
						v-model.lazy.trim="title"
						class="form-control" />
					<small id="titleHelp" class="form-text text-muted">The title of the task - maximum length of 32 characters</small>
				</div>
				<div class="form-group">
					<label for="description-input">Description</label>
					<input
						type="text"
						id="description-input"
						maxlength="256"
						autocomplete="off"
						v-model.lazy.trim="description"
						class="form-control" />
					<small id="descriptionHelp" class="form-text text-muted">The description of the task (optional) - maximum length of 256 characters</small>
				</div>
				<div class="form-group">
					<label for="status-input-addtask">Status</label>
					<select id="status-input-addtask" v-model.lazy.trim="status" class="form-control">
						<option v-for="status in statuses" 
								:key="status.name" 
								:value="status.id">{{ status.name }}</option>
					</select>
					<small id="statusHelp" class="form-text text-muted">The status of the task</small>
				</div>
				<div class="form-group">
					<label for="date-input">Due date</label>
					<DateTimePicker id="date-input" @date-picked="datePicked" />
					<small id="dateHelp" class="form-text text-muted">The due date of the task</small>
				</div>
				<button type="submit" class="btn btn-primary">Add</button>
			</form>
		</div>
	</div>
</template>

<script>
import DateTimePicker from "./DateTimePicker.vue";

export default {
	components: {
		DateTimePicker
	},
	data() {
		return {
			errors: [],
			title: null,
			date: null,
			status: null,
			statuses: [],
		};
	},
	async mounted() {
		const { data } = await this.axios.get(
			"http://127.0.0.1:8000/api/statuses/", {withCredentials: false,}
		);
		this.statuses = data;
	},
	methods: {

		checkForm() {
			if (this.title && this.date && this.status) {
				return true;
			}

			this.errors = [];

			if (!this.title) {
				this.errors.push('Title required.');
			}
			if (!this.status) {
				this.errors.push('Status required.');
			}
			if (!this.date) {
				this.errors.push('Date required.');
			}
			return false;
		},

		datePicked(date) {
			this.date = date;
		},

		onSubmit() {
			if (!this.checkForm()) {
				// errors are set in checkForm
				return
			}

			// format the date
			var date  = new Date(this.date);
			const day = date.getDate();
			const month = date.getMonth() + 1;
			const year = date.getFullYear();
			const hours = date.getHours();
			const minutes = date.getMinutes();
			this.date = `${year}-${month}-${day}T${hours}:${minutes}`;

			this.axios.post('http://127.0.0.1:8000/api/task_list/', 
			{
				title: this.title, 
				description: this.description, 
				status: this.status, 
				due_date: this.date
			}, 
			{
				headers: {
				'Content-Type': 'multipart/form-data'
				}
			}
			)
			.then(
				this.$emit("task-added")
			)
		},
	},
};
</script>