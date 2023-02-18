<template>
	<div id="app" class="container my-3">
		<h3>Vue Axios example</h3>

		<div class="card mt-3">
			<div class="card-header">Vue Axios GET</div>
			<div class="card-body">
				<div class="input-group input-group-sm">
					<button class="btn btn-sm btn-primary" @click="getAllData">
						Get All
					</button>

					<input
						type="text"
						ref="get_id"
						class="form-control ml-2"
						placeholder="Id"
					/>
					<div class="input-group-append">
						<button class="btn btn-sm btn-primary" @click="getDataById">
							Get by Id
						</button>
					</div>

					<button class="btn btn-sm btn-warning ml-2" @click="clearGetOutput">
						Clear
					</button>
				</div>

				<div v-if="getResult" class="alert alert-secondary mt-2" role="alert">
					<pre>{{ getResult }}</pre>
				</div>
			</div>
		</div>

		<div class="card mt-3">
			<div class="card-header">Vue Axios POST</div>
			<div class="card-body">
				<div class="form-group">
					<input
						type="text"
						class="form-control"
						ref="post_title"
						placeholder="Judul"
					/>
				</div>
				<div class="form-group">
					<input
						type="text"
						class="form-control"
						ref="post_description"
						placeholder="Deskripsi"
					/>
				</div>
				<div class="form-group">
					<div class="example ex1">
						<h4>Select Status</h4>
						<select v-model="selectedValue" ref="mySelect">
							<option value='true'>Selesai</option>
							<option value=''>Belum selesai</option>
						</select>
					</div>
				</div>
				<button class="btn btn-sm btn-primary" @click="postData">
					Post Data
				</button>
				<button class="btn btn-sm btn-warning ml-2" @click="clearPostOutput">
					Clear
				</button>

				<div v-if="postResult" class="alert alert-secondary mt-2" role="alert">
					<pre>{{ postResult }}</pre>
				</div>
			</div>
		</div>

		<div class="card mt-3">
			<div class="card-header">Vue Axios PUT</div>
			<div class="card-body">
				<div class="form-group">
					<input
						type="text"
						class="form-control"
						ref="put_id"
						placeholder="Id"
					/>
				</div>
				<div class="form-group">
					<input
						type="text"
						class="form-control"
						ref="put_title"
						placeholder="Title"
					/>
				</div>
				<div class="form-group">
					<input
						type="text"
						class="form-control"
						ref="put_description"
						placeholder="Description"
					/>
				</div>
        <div class="form-group">
					<div class="example ex1">
						<h4>Select Status</h4>
						<select v-model="selectedValue" ref="mySelect">
							<option value='true'>Selesai</option>
							<option value=''>Belum selesai</option>
						</select>
					</div>
				</div>
				<button class="btn btn-sm btn-primary" @click="putData">
					Update Data
				</button>
				<button class="btn btn-sm btn-warning ml-2" @click="clearPutOutput">
					Clear
				</button>

				<div v-if="putResult" class="alert alert-secondary mt-2" role="alert">
					<pre>{{ putResult }}</pre>
				</div>
			</div>
		</div>

		<div class="card mt-3">
			<div class="card-header">Vue Axios DELETE</div>
			<div class="card-body">
				<div class="input-group input-group-sm">

					<input
						type="text"
						ref="delete_id"
						class="form-control ml-2"
						placeholder="Id"
					/>
					<div class="input-group-append">
						<button class="btn btn-sm btn-danger" @click="deleteDataById">
							Delete by Id
						</button>
					</div>

					<button
						class="btn btn-sm btn-warning ml-2"
						@click="clearDeleteOutput"
					>
						Clear
					</button>
				</div>

				<div
					v-if="deleteResult"
					class="alert alert-secondary mt-2"
					role="alert"
				>
					<pre>{{ deleteResult }}</pre>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
import http from "./http-common";
export default {
	name: "App",
	data() {
		return {
			getResult: null,
			postResult: null,
			putResult: null,
			deleteResult: null,
		};
	},
	methods: {
		fortmatResponse(res) {
			return JSON.stringify(res, null, 2);
		},
		async getAllData() {
			try {
				const res = await http.get("/tugas");
				const result = {
					status: res.status + "-" + res.statusText,
					headers: res.headers,
					data: res.data,
				};
				this.getResult = this.fortmatResponse(result);
			} catch (err) {
				this.getResult = this.fortmatResponse(err.response?.data) || err;
			}
		},
		async getDataById() {
			const id = this.$refs.get_id.value;
			if (id) {
				try {
					const res = await http.get(`/tugas/${id}`);
					const result = {
						status: res.status + "-" + res.statusText,
						headers: res.headers,
						data: res.data,
					};
					// const result = {
					//   data: res.data,
					//   status: res.status,
					//   statusText: res.statusText,
					//   headers: res.headers,
					//   config: res.config,
					// };
					this.getResult = this.fortmatResponse(result);
				} catch (err) {
					this.getResult = this.fortmatResponse(err.response?.data) || err;
				}
			}
		},
		async postData() {
			const postData = {
				judul: this.$refs.post_title.value,
				deskripsi: this.$refs.post_description.value,
				selesai: Boolean(this.$refs.mySelect.value),
				//get value from option box?
			};
      console.log(postData);
			try {
				const res = await http.post("/tugas", postData, {});
				const result = {
					status: res.status + "-" + res.statusText,
					headers: res.headers,
					data: res.data,
				};
				this.postResult = this.fortmatResponse(result);
			} catch (err) {
				this.postResult = this.fortmatResponse(err.response?.data) || err;
			}
		},
		async putData() {
			const { put_id, put_title, put_description } = this.$refs;
			const id = put_id.value;
			if (id) {
				const putData = {
        judul: put_title.value,
				deskripsi: put_description.value,
				selesai: Boolean(this.$refs.mySelect.value),
				};
				try {
					const res = await http.patch(`/tugas/${id}`, putData, {
						headers: {
							"x-access-token": "token-value",
						},
					});
					const result = {
						status: res.status + "-" + res.statusText,
						headers: res.headers,
						data: res.data,
					};
					this.putResult = this.fortmatResponse(result);
				} catch (err) {
					this.putResult = this.fortmatResponse(err.response?.data) || err;
				}
			}
		},
		async deleteDataById() {
			const id = this.$refs.delete_id.value;
			if (id) {
				try {
					const res = await http.delete(`/tugas/${id}`);
					const result = {
						status: res.status + "-" + res.statusText,
						headers: res.headers,
						data: res.data,
					};
					this.deleteResult = this.fortmatResponse(result);
				} catch (err) {
					this.deleteResult = this.fortmatResponse(err.response?.data) || err;
				}
			}
		},
		clearGetOutput() {
			this.getResult = null;
		},
		clearPostOutput() {
			this.postResult = null;
		},
		clearPutOutput() {
			this.putResult = null;
		},
		clearDeleteOutput() {
			this.deleteResult = null;
		},
	},
};
</script>
<style>
#app {
	max-width: 600px;
	margin: auto;
}
</style>
