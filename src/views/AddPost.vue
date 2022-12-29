<template>
  <div class="card">
    <div class="card-content is-size-6">      
      <div class="columns is-vcentered">
        <div class="column is-10 has-text-centered">
          <p class="is-size-3">Add Post</p>
        <div class="field">
          <label class="label has-text-left">Title</label>
          <input class="input is-primary" type="text" placeholder="Title" v-model="title">
        </div>
        <div class="field">
          <label class="label label has-text-left">Description</label>
          <input class="input is-primary" type="text" placeholder="Description" v-model="description">
        </div>
        <div class="field">
            <DropZone v-show="!showFile" @drop.prevent="drop" @change="selectedFile" />
            <div v-show="showFile">
              <p>File: {{ dropzoneFile.name }}</p>
                <img :src="imageUrl" width="256" />
            </div>
            <progress v-show="showProgress" class="progress pt-3" :value="progress" max="100">{{ progress }}</progress>
            </div>
            <div class="field pt-3">
              <button @click="uploadFile" class="button is-info">Submit</button>
        </div>
      </div>
    </div>
  </div>
</div>  

</template>

<script setup>
  import {ref} from 'vue'
  import axios from 'axios'
  import DropZone from "@/components/DropZone.vue"
  import { useStorePosts } from '@/stores/storePosts'

  const storePosts = useStorePosts()

  const title = ref('')
  const description = ref('')
  const dropzoneFile = ref('')
  const progress = ref('')
  const showProgress = ref(false)
  const showFile = ref(false)

  const imageUrl = ref('')

  let image = null

  const showImage = () => {
    image = dropzoneFile.value
    const reader = new FileReader() 
    reader.addEventListener(
      'load',
      () => {
        imageUrl.value = reader.result
      })
    reader.readAsDataURL(image)
  }

  const drop = (event) =>  {
        dropzoneFile.value = event.dataTransfer.files[0]
        showImage()
        showFile.value = true
  }

  const selectedFile = () => {
        dropzoneFile.value = document.querySelector(".dropzoneFile").files[0]
        showImage()
        showFile.value = true
  }

  const uploadFile = () => {
    showProgress.value = true
    const cloudinaryUploadURL = 'https://api.cloudinary.com/v1_1/dsyohxfyg/upload'
    const formData = new FormData()
    formData.append("upload_preset", 'esh3pivg')
    formData.append("file", image)

    const config = {
      onUploadProgress: (progressEvent) => {
        progress.value = Math.round((progressEvent.loaded * 100.0) / progressEvent.total)
      }
 
    }
    axios.post(cloudinaryUploadURL, formData, config)
    .then ((response) => {
    let uploadUrl = response.data.url
      storePosts.addPost(title.value, description.value, uploadUrl)  
      
    })
    .catch( (err) => {
      console.error('axios error', err)
    })
    .finally(() => {
      setTimeout(() => {  
        showFile.value = false
        showProgress.value = false
      }, 1000)
      
    })
       
  }
</script>

<style lang="scss" scoped>
.home {
  .file-info {
    margin-top: 32px;
  }
}
</style>