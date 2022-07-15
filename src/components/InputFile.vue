<template>
  <div class="items-stretch justify-start">
    <div class="flex py-2">
      <label>
          <input type="file" @change.stop="toggleUpload" multiple
          class="text-sm text-grey-500
          file:mr-5 file:py-2 file:px-6
          file:rounded-full file:border-0
          file:text-sm file:font-medium
          file:bg-blue-700 file:text-white
          hover:file:cursor-pointer hover:file:bg-blue-50
          hover:file:text-amber-700" />
      </label>
    </div>
    <div class="flex flex-wrap py-2">
      <viewer class="flex flex-wrap py-2" :images="fileManager.urls">
        <img
          class="p-1"
          v-for="url in fileManager.urls"
          style="width: 100px; height: 100px"
          :src="url"
          :key="src"
          fit="cover"
        />
      </viewer>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, reactive } from 'vue'

export default defineComponent({
  name: 'FileInput',
  components: {
  },
  props: {
  },
  methods: {
    show() {
      this.$viewerApi({
        images: this.images,
      })
    },
    toggleUpload($event) {
      this.fileManager.file = $event.target.files
      this.$emit('change', this.fileManager.file)
      Promise.all(Array.from(this.fileManager.file).slice(0, 50).map(file => {
        return new Promise((resolve, reject) => {
          if (file.type.includes('image')) {
            let fileReader = new FileReader();
            fileReader.onloadend = (e) => {
              resolve(e.target.result)
            };
            fileReader.readAsDataURL(file)
          } else {
            resolve(null)
          }
        })
      }))
      .then((fileUrl) => {
        this.fileManager.urls = fileUrl.filter(url => url !== null)
      })
    },
  },
  setup() {
    const fileManager = reactive({
      urls: [],
      files: []
    })

    return {
      fileManager
    }
  }
})
</script>
