<template>
	<select id="status-input" @change="onChange($event)" class="form-control">
		<option v-for="status in statuses" 
						:key="status.name" 
						:value="status.id" 
						:selected="status.id === this.statusId">{{ status.name }}</option>
	</select>
</template>

<script>
export default {
	props: ['taskId', 'statusId', 'statuses'],

	methods: {
		onChange(event) {
			this.axios.put('http://127.0.0.1:8000/api/task_list/' + this.taskId + '/', 
				{status: event.target.value}, 
				{
				headers: {
					'Content-Type': 'multipart/form-data'
				}
				}
			)
			.catch(error => console.log(error));
		}
	}
}
</script>