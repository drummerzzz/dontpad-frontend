<template>
  <q-page class="row items-center justify-evenly full-height">
    <div class="col-12">
      <div class="row justify-center q-gutter-sm">
        <q-input
          dark
          v-model="url"
          outlined autofocus
          class="col-8 text-h6"
          color="white" label="Digite o path do artigo"/>
          <q-btn @click="goTo" icon="arrow_right" color="blue-9" />
      </div>
    </div>
  </q-page>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const url = ref('')

const goTo = () => {
  if (!url.value) return
  window.location.replace(url.value)
}

</script>
