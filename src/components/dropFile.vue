<template>
  <div class="wrapper">
    <h4 class="wrapper-heading">Upload your files with dragging in the box</h4>
    <div
      class="drop-container"
      @dragstart="loading = true"
      @drop.prevent="handleDrop"
      :style="loading && 'border-color: green;'"
      @dragover.prevent="handleDragOver"
    >
      <label for="fileInput" class="file-label">
        <div v-if="loading">Release to drop files here.</div>
        <div v-else>Drop files here .</div>
      </label>
    </div>

    <div v-if="loading" class="progress-container">
      <div class="progress-bar" :style="{ width: progress + '%' }"></div>
      <p>{{ progress }}%</p>
    </div>
    <div v-if="droppedFiles.length > 0" class="file-list">
      <h2>Dropped Files:</h2>
      <ul class="list-items">
        <li
          v-for="(file, index) in droppedFiles"
          :key="index"
          class="preview-card"
        >
          <img
            :src="generateURL(file)"
            v-if="generateURL(file)"
            class="preview-img"
          />
          <p class="info">
            {{ file.name }}
          </p>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      progress: 0,
      loading: false,
      droppedFiles: [],
    };
  },
  methods: {
    handleDrop(event) {
      event.preventDefault();
      this.loading = true;

      const files = event.dataTransfer.files;

      this.handleFiles(files);
    },

    handleDragOver(event) {
      event.preventDefault();
    },
    generateURL(file) {
      let fileSrc = URL.createObjectURL(file);
      setTimeout(() => {
        URL.revokeObjectURL(fileSrc);
      }, 1000);
      return fileSrc;
    },

    handleFiles(files) {
      const totalSize = Array.from(files).reduce(
        (acc, file) => acc + file.size,
        0
      );
      let processedSize = 0;

      Array.from(files).forEach((file) => {
        processedSize += file.size;
        this.progress = Math.min((processedSize / totalSize) * 100, 100);

        if (processedSize === totalSize) {
          setTimeout(() => {
            // All files processed
            this.loading = false;

            this.droppedFiles = Array.from(files);
          }, 1500);
        }
        // Simulated delay per file processing
      });
    },
  },
};
</script>
<style scoped>
.drop-container {
  border: 2px dashed #ccc;
  padding: 50px;
  margin: 10px 0px;
  width: 100%;
  background: #c5e2ee;
  height: 20vh;
  justify-content: center;
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
  align-items: center;
  text-align: center;
}
.wrapper-heading {
  width: 100%;
  text-align: center;
}
.hidden-input {
  opacity: 0;
  overflow: hidden;
  position: absolute;
  width: 1px;
  height: 1px;
}

.file-list {
  width: 100%;
}
.list-items {
  width: 100%;
  display: flex;
  justify-content: flex-start;

  flex-wrap: wrap;
}
.list-items li {
  padding: 0 20px;
}
.progress-container {
  width: 100%;
  position: relative;
  height: 20px;
  width: 500px;
  border-radius: 20px;
  margin-top: 10px;
}

.progress-bar {
  height: 100%;

  background-color: #3eaf7c;
  border-radius: 20px;

  width: 0;
  position: absolute;
}

.progress-container p {
  position: absolute;
  top: 0;
  left: 50%;
  transform: translateX(-50%);
  color: #333;
}
.preview-card {
  display: flex;
  border: 1px solid #a2a2a2;
  flex-wrap: wrap;
  padding: 5px;
  margin-left: 5px;
}

.preview-img {
  width: 50px;
  max-width: 80%;
  height: 50px;
  vertical-align: center;
  border-radius: 5px;
  border: 1px solid #a2a2a2;
  background-color: #a2a2a2;
}
.info{
    width: 100%;
}
</style>
