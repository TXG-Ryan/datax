<template>
  <div class="application-wrapper">
    <Navigation />
    <div class="pagecomponent">
      <router-view/>
    </div>
  </div>
</template>

<script>
import Navigation from './components/Navigation.vue'

export default {
  components: {
    Navigation,
  },
  setup() {
    
  },
}
</script>

<style>
</style>
