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
            <th style="width: 20%;">Date</th>
            <th>Content file</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(file, i) in files" :key="i">
            <td>{{ new Date(file.datetime).toLocaleString() }}</td>
            <td class="p-1">
              <textarea class="form-control" v-model="file.data"> </textarea>
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
  methods: {
    async upload() {
      this.loading = true;
      for (let i = 0; i < this.$refs.files.files.length; i++) {
        let dataForm = new FormData();
        let file = this.$refs.files.files[i];
        dataForm.append(`my-pdf`, file);
        let res = await fetch('http://localhost:3000/pdf/upload', {
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
      let res = await fetch('http://localhost:3000/pdf');
      this.files = await res.json();
      this.loading = false;
    },
  },
  mounted() {
    this.loadFiles();
  },
};
</script>
