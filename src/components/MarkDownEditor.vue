<template>
<div>
  <vue-markdown>this is the default slot</vue-markdown>
</div>
</template>

<script>
import VueMarkdown from 'vue-markdown'

export default {
  components: {
    // eslint-disable-next-line @typescript-eslint/no-unsafe-assignment
    'vue-markdown': VueMarkdown
  }
}

</script>
