<template>
	<div class="container">
		<div class="table-responsive">
			<center>
				<h1>School Table</h1>
			</center>
			<div class="btn-group" role="group" aria-label="Third group">
				<button type="button" class="btn btn-primary" @click="onNewButtonClick()">+</button>
			</div>
			<br /><br />
			<table class="table table-bordered table striped">
				<thead class="thead-dark">
					<tr>
						<th>Name</th>
						<th>Code</th>
						<th>School Type</th>
					</tr>
				</thead>
				<tbody>
					<tr v-for="item in schoolList" :key="item.id">
						<td>{{ item.name }}</td>
						<td>{{ item.code }}</td>
						<td>{{ item.schoolType.name }}</td>
						<td>
							<button class="btn btn-info btn-sm" @click="onRowClick(item)" type="button">EDIT</button>
							<button class="btn btn-warning btn-sm" @click="deleteSchool(item.id)" type="button">DELETE</button>
						</td>
					</tr>
				</tbody>
			</table>
		</div>

		<form v-show="showForm">
			<div class="container">
				<div class="form row">
					<div class="form-group col-md-3">
						<input v-model="selectedSchool.id" class="input-text" type="number" placeholder="Id" disabled />
					</div>
					<div class="form-group col-md-3">
						<input v-model="selectedSchool.name" class="input-text" type="text" placeholder="Name" />
					</div>
					<div class="form-group col-md-3">
						<input v-model="selectedSchool.code" class="input-text" type="text" placeholder="Code" />
					</div>
					<div class="form-group col-md-3">
						<select v-model="selectedSchool.schoolType.id">
							<option v-for="item in schoolTypeList" v-bind:value="item.id" v-bind:key="item.id"> {{ item.name }} </option>
						</select>
					</div>
				</div>
				<br />
				<div class="d-grid gap-2 d-md-block">
					<button class="btn btn-success" id="btnSave" @click="saveSchool()" type="button">SAVE</button>
					<button class="btn btn-danger" type="cancel" @click="showForm = false">CANCEL</button>
				</div>
			</div>
		</form>
		<br /><br />
		<center>
			<div class="d-grid gap-2 d-md-block">
				<select v-model="size">
					<option v-bind:value="1"> 1 </option>
					<option v-bind:value="2"> 2 </option>
					<option v-bind:value="3"> 3 </option>
					<option v-bind:value="4"> 4 </option>
					<option v-bind:value="5"> 5 </option>
				</select>
				<button class="btn btn-secondary" type="cancel" id="btnPrev" @click="page > 0 ? page-- : page">PREV</button>
				<span>{{ page + 1 }} in {{ pageData.totalPages }}</span>
				<button class="btn btn-secondary" id="btnNext" @click="page + 1 < pageData.totalPages ? page++ : page" type="button">NEXT</button>
			</div>
		</center>
	</div>
</template>

<script>
export default {
	name: "school",
	data: function() {
		return {
			schoolList: [],
			schoolTypeList: [],
			selectedSchool: { schoolType: {} },
			showForm: false,
			page: 0,
			size: 1,
			pageData: {},
		};
	},

	watch: {
		page: function() {
			this.getSchoolList();
		},
		size: function() {
			this.getSchoolList();
		},
	},

	methods: {
		async getSchoolList() {
			const response = await this.axios.get("http://localhost:8080/school?size=" + this.size + "&page=" + this.page);
			this.pageData = response.data.page;
			this.schoolList = response.data._embedded.schools;
		},
		async getSchoolTypes() {
			const response = await this.axios.get("http://localhost:8080/schoolType");
			this.schoolTypeList = response.data._embedded.schoolTypes;
		},
		saveSchool() {
			if (this.selectedSchool.id == null || this.selectedSchool.id == "") this.addSchool();
			else this.updateSchool();
		},
		async addSchool() {
			const response = await this.axios.post("http://localhost:8080/school/", this.selectedSchool);
			this.selectedSchool = response.data;
			this.getSchoolList();
		},
		async updateSchool() {
			await this.axios.put("http://localhost:8080/school/" + this.selectedSchool.id, this.selectedSchool);
			this.getSchoolList();
		},
		async deleteSchool(id) {
			await this.axios.delete("http://localhost:8080/school/" + id);
			this.getSchoolList();
		},
		onNewButtonClick() {
			this.selectedSchool = { schoolType: {} };
			this.showForm = true;
		},
		onRowClick(item) {
			this.selectedSchool = JSON.parse(JSON.stringify(item));
			this.showForm = true;
		},
	},
	mounted() {
		this.getSchoolList();
		this.getSchoolTypes();
	},
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
	margin: 40px 0 0;
}
ul {
	list-style-type: none;
	padding: 0;
}
li {
	display: inline-block;
	margin: 0 10px;
}
a {
	color: #42b983;
}
</style>
