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

  methods: {
    switchView (target) {
      this.currentView = target
      document.body.scrollTop = 0
    }
  },

  ready () {
    const self = this
    scrollHandler = (evt) => {
      self.scrollTop = (evt.target.scrollingElement || (document.documentElement || document.body.parentNode)).scrollTop || 0
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
      <p>A dropdown time picker for <span class="version">Vue 1.x</span> with flexible time format support</p>
    </header>
    <nav class="top-nav" :class="{stick: scrollTop > 150}">
      <span class="title">Vue Timepicker</span>
      <ul>
        <li><a @click="switchView('samples')" :class="{active: currentView === 'samples'}">Common Usage</a></li>
        <li><a @click="switchView('playground')" :class="{active: currentView === 'playground'}">Playground</a></li>
        <li><a href="https://github.com/phoenixwong/vue-timepicker" target="_blank">Documentation</a></li>
      </ul>
      <span class="v2x"><a href="https://phoenixwong.github.io/vue2-timepicker/" target="_blank">Vue 2.x Version</a> &raquo;</span>
    </nav>
    <main class="content" :class="{'nav-affixed': scrollTop > 150}">
      <component :is="currentView" transition="fade" transition-mode="out-in">
        <div slot="footer-links">
          <h3 class="title"><a class="anchor" id="more">#</a>More complex usage</h3>
          <div class="description">
            <p>Didn't find what you need? Please check the <a @click="switchView('playground')">Playground</a> or <a href="https://github.com/phoenixwong/vue-timepicker" target="_blank">Documentation</a> for more inspiration.</p>
          </div>
        </div>
      </component>
    </main>
  </div>
</template>

<style lang="sass">
@import "./assets/demo.scss";
</style>
