<script>
import Samples from 'components/Samples.vue'
import Playground from 'components/Playground.vue'

var scrollHandler

export default {
  name: 'App',
  components: {
    'samples': Samples,
    'playground': Playground
  },

  data () {
    return {
      currentView: 'samples',
      scrollTop: 0
    }
  },

  ready () {
    const self = this
    scrollHandler = (evt) => {
      self.scrollTop = evt.target.scrollingElement.scrollTop || 0
    }
    window.addEventListener('scroll', scrollHandler)
  },

  beforeDestroy () {
    window.removeEventListener('scroll', scrollHandler)
  }
}
</script>

<template>
  <div class="main-wrapper">
    <header v-cloak>
      <h1>Vue Timepicker</h1>
      <p>A dropdown time picker for Vue.js. With flexible time format support</p>
    </header>
    <nav class="top-nav" :class="{stick: scrollTop > 150}">
      <span class="title">Vue Timepicker</span>
      <ul>
        <li><a @click="currentView = 'samples'" :class="{active: currentView === 'samples'}">Common Usage</a></li>
        <li><a @click="currentView = 'playground'" :class="{active: currentView === 'playground'}">Playground</a></li>
        <li><a href="https://github.com/phoenixwong/vue-timepicker" target="_blank">Documentation</a></li>
      </ul>
    </nav>
    <main class="content" :class="{'nav-affixed': scrollTop > 150}">
      <component :is="currentView" transition="fade" transition-mode="out-in"></component>
    </main>
  </div>
</template>

<style lang="sass">
@import "./assets/demo.scss";
</style>
