<template id="#app-template">
  <div id="app">
    <Repos />
  </div>
</template>

<script>
import Repos from './components/Repos'

export default {
  template: '#app-template',
  name: 'app',
  components: {
    Repos
  }
}
</script>
