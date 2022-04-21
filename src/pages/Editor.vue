<template>
  <q-page padding class="bg-background1">
    <div class="row flex justify-center">
      <q-editor
        v-model="text"
        flat
        fullscreen
        :disable="isLoading"
        class="q-ma-xl bg-background1"
        toolbar-color="white"
        toolbar-bg="background1"
        style="color: white;"
        :definitions="{
          save: {
            tip: 'Save article',
            icon: 'save',
            label: 'Save',
            class: 'text-h6',
            handler: onSave
          }
        }"
        :toolbar="[
          [
            {
              label: $q.lang.editor.align,
              icon: $q.iconSet.editor.align,
              fixedLabel: true,
              list: 'only-icons',
              options: ['left', 'center', 'right', 'justify']
            },
            {
              label: $q.lang.editor.align,
              icon: $q.iconSet.editor.align,
              fixedLabel: true,
              options: ['left', 'center', 'right', 'justify']
            }
          ],
          ['bold', 'italic', 'strike', 'underline', 'subscript', 'superscript'],
          ['token', 'hr', 'link'],
          ['print', 'fullscreen'],
          [
            {
              label: $q.lang.editor.formatting,
              icon: $q.iconSet.editor.formatting,
              list: 'no-icons',
              color: 'white',
              options: [
                'p',
                'h1',
                'h2',
                'h3',
                'h4',
                'h5',
                'h6',
              ]
            },
            {
              label: $q.lang.editor.fontSize,
              icon: $q.iconSet.editor.fontSize,
              fixedLabel: true,
              fixedIcon: true,
              list: 'no-icons',
              options: [
                'size-1',
                'size-2',
                'size-3',
                'size-4',
                'size-5',
                'size-6',
                'size-7'
              ]
            },
            {
              label: $q.lang.editor.defaultFont,
              icon: $q.iconSet.editor.font,
              fixedIcon: true,
              list: 'no-icons',
              options: [
                'default_font',
                'arial',
                'arial_black',
                'comic_sans',
                'courier_new',
                'impact',
                'lucida_grande',
                'times_new_roman',
                'verdana'
              ]
            },
            'removeFormat'
          ],
          ['quote', 'unordered', 'ordered', 'outdent', 'indent'],

          ['undo', 'redo', 'viewsource'],
          ['save']
        ]"
        :fonts="{
          arial: 'Arial',
          arial_black: 'Arial Black',
          comic_sans: 'Comic Sans MS',
          courier_new: 'Courier New',
          impact: 'Impact',
          lucida_grande: 'Lucida Grande',
          times_new_roman: 'Times New Roman',
          verdana: 'Verdana'
        }" />
      </div>
  </q-page>
</template>

<script setup lang="ts">
import { useQuasar } from 'quasar'
import { useRoute } from 'vue-router'
import { ref, onMounted, watch } from 'vue'
import axios, { AxiosResponse } from 'axios'

const $q = useQuasar()
const $route = useRoute()
const { url } = $route.params
const text = ref('')
const isLoading = ref(true)

interface Article {
  url: string
  text: string
  'created_at': string
  'updated_at': string
}

const baseUrl = 'https://dontpad-backend-production.up.railway.app'

const onLoad = () => {
  $q.notify({
    message: 'Loading...',
    color: 'white',
    textColor: 'black',
    timeout: 1000,
    position: 'top-right'
  })
  // eslint-disable-next-line @typescript-eslint/restrict-template-expressions
  axios.get(`${baseUrl}/articles/${url}`).then((response: AxiosResponse<Article>) => {
    text.value = response.data?.text
  }).catch(() => {
    $q.notify({
      message: 'Could not load',
      type: 'negative',
      position: 'top'
    })
  }).finally(() => {
    isLoading.value = false
  })
}

const onSave = () => {
  $q.notify({
    message: 'Saving',
    color: 'white',
    textColor: 'black',
    timeout: 50,
    position: 'top-right'
  })
  // eslint-disable-next-line @typescript-eslint/restrict-template-expressions
  axios.post(`${baseUrl}/articles/${url}`, {
    url,
    text: text.value
  }).then(() => {
    console.log(`saved at: ${new Date().toISOString()}`)
  }).catch(() => {
    $q.notify({
      message: 'Could not save',
      type: 'negative',
      position: 'top-left'
    })
  })
}

onMounted(() => {
  onLoad()
})

// eslint-disable-next-line no-undef
let timeout: NodeJS.Timeout

watch(() => text.value, () => {
  if (isLoading.value) return
  const delay = 3000 // 3s
  if (timeout) clearTimeout(timeout)
  timeout = setTimeout(() => {
    onSave()
  }, delay)
})
</script>
