<template>
  <div class="container mt-3">
    <Loading v-if="loading" />

    <div class="card mb-3">
      <div class="card-header ">Upload</div>
      <div class="card-body">
        <div class="d-flex">
          <div>
            <label>
              <input type="file" class="d-none" accept=".pdf" multiple ref="files" @change="handleFiles" />
              <span class="btn btn-outline-primary mt-1 mr-2">Select file</span>
            </label>
          </div>
          <template v-if="count > 0">
            <div class="flex-grow-1">
              <div class="alert alert-success mr-2 mb-0">
                File Selected!
              </div>
            </div>
            <div>
              <button class="btn btn-outline-primary mt-1" @click="upload">Upload</button>
            </div>
          </template>
        </div>
      </div>
    </div>

    <div class="card">
      <div class="card-header">Files</div>
      <table class="table table-bordered mb-0">
        <thead>
          <tr>
            <th style="width: 5%;">Action</th>
            <th style="width: 20%;">Date</th>
            <th>Content file</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(file, i) in files" :key="file.datetime">
            <td>
              <a href="#" @click.prevent="deleteFile(i)">Delete</a>
            </td>
            <td>{{ new Date(file.datetime).toLocaleString() }}</td>
            <td class="p-1">
              <textarea class="form-control" v-model="file.data" rows="5"> </textarea>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import Loading from '@/components/Loading.vue';

export default {
  name: 'App',
  components: { Loading },
  data: () => ({
    count: 0,
    files: [],
    loading: false,
  }),
  computed: {
    backendUrl() {
      if (process.env.NODE_ENV == 'development') {
        return 'http://localhost:3000';
      }
      return 'https://extractify-backend-1.herokuapp.com';
    },
  },
  methods: {
    async upload() {
      this.loading = true;
      for (let i = 0; i < this.$refs.files.files.length; i++) {
        let dataForm = new FormData();
        let file = this.$refs.files.files[i];
        dataForm.append(`file-pdf`, file);
        let res = await fetch(`${this.backendUrl}/pdf`, {
          method: 'POST',
          body: dataForm,
        });
        let data = await res.json();
        this.files.push(data);
      }
      this.clear();
      this.loading = false;
    },
    clear() {
      this.$refs.files.value = '';
      this.count = 0;
    },
    handleFiles(event) {
      this.count = event.target.files.length;
    },
    async loadFiles() {
      this.loading = true;
      let res = await fetch(`${this.backendUrl}/pdf`);
      this.files = await res.json();
      this.loading = false;
    },
    async deleteFile(index) {
      let file = this.files[index];
      await fetch(`${this.backendUrl}/pdf/${file.datetime}`, { method: 'DELETE' });
      this.files.splice(index, 1);
    },
  },
  mounted() {
    this.loadFiles();
  },
};
</script>
