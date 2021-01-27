<template>
<main>
  <Nuxt />
  <Footer />
</main>
</template>

<script>
import Footer from '~/components/Footer'

export default {
  components: {
    Footer
  }
}
</script>

<style lang="scss">
@import '~/assets/common';
body {
  background: #e0e0e0;
}
</style>
