<template>

  <transition v-if="show" name="modal">
    <div class="modal-mask">
      <div class="modal-container">
        <form class="pure-form" @submit.prevent="upload">
          <legend>Upload a picture</legend>
          <fieldset>
            <input v-model="title" placeholder="Title" />
          </fieldset>

          <fieldset>
            <textarea
              v-model="description"
              placeholder="Description"
            ></textarea>
          </fieldset>

          <fieldset>
            <div class="imageInput" @click="chooseImage">
              <img v-if="url" :src="url" />
              <div v-if="!url" class="placeholder">Choose an Image</div>
              <input
                class="fileInput"
                ref="fileInput"
                type="file"
                @input="fileChanged"
              />
            </div>
            <p v-if="error" class="error">{{ error }}</p>
          </fieldset>

          <fieldset class="buttons">
            <button type="button" @click="close" class="pure-button">
              Close
            </button>
            <button type="submit" class="pure-button pure-button-primary right">
              Upload
            </button>
          </fieldset>

        </form>
      </div>
    </div>
  </transition>
</template>

<script>
import axios from "axios";
export default {
  name: "Uploader",
  props: {
    show: Boolean,
  },
  data() {
    return {
      title: "",
      description: "",
      url: "",
      file: null,
      error: "",
    };
  },
  methods: {
    fileChanged(event) {
      this.file = event.target.files[0];
      this.url = URL.createObjectURL(this.file);
    },
    close() {
      this.$emit("close");
    },
    chooseImage() {
      this.$refs.fileInput.click();
    },
    async upload() {
      try {
        const formData = new FormData();
        formData.append("photo", this.file, this.file.name);
        formData.append("title", this.title);
        formData.append("description", this.description);
        await axios.post("/api/photos", formData);
        this.file = null;
        this.url = "";
        this.title = "";
        this.description = "";
        this.$emit("uploadFinished");
      } catch (error) {
        this.error = "Error: " + error.response.data.message;
      }
    },
  },
};
</script>

<style scoped>
/* Modals */
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.3);
  transition: opacity 0.5s ease;
  display: flex;
  transition: background 0.2s ease-in-out, height 1s ease-in-out;
  transition: height 0.2s ease-in-out, width 0.2s ease-in-out;
  justify-content: center;
  transition-timing-function: cubic-bezier(0.64, 0.57, 0.67, 1.53);
}

.modal-container {
  width: 50%;
  height: max-content;
  margin-top: 80px;
  padding: 20px 30px;
  background-color: #fff;
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.5s ease;
}

.modal-enter {
  opacity: 0;
}

.modal-leave-active {
  opacity: 0;
}

.modal-enter .modal-container,
.modal-leave-active .modal-container {
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}

form {
  font-size: 11pt;
}

input {
  width: 100%;
}

textarea {
  width: 100%;
  height: 100px;
}

.placeholder {
  background: #f0f0f0;
  width: 200px;
  height: 125px;
  display: flex;
  justify-content: center;
  align-items: center;
  color: #333;
  font-size: 14px;
  cursor: pointer;
}

.placeholder:hover {
  background: #e0e0e0;
}

.fileInput {
  display: none;
}

img {
  width: 200px;
}

.buttons {
  display: flex;
  justify-content: space-between;
}

</style>
