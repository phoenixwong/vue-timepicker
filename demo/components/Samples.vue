<script>
import VueTimepicker from 'src/vue-timepicker.vue'

export default {
  name: 'Samples',
  components: [VueTimepicker],
  data () {
    return {
      yourData: {
        hh: '03',
        mm: '10',
        ss: '00',
        a: 'am'
      },
      yourFormat: 'hh:mm:ss a'
    }
  },
  methods: {
    refreshAllHighlight () {
      if (!this.$el) { return }
      const codeBlocks = this.$el.querySelectorAll('pre code')
      codeBlocks.forEach((block) => {
        window.hljs.highlightBlock(block)
      })
    }
  },
  compiled () {
    const self = this
    this.$nextTick(() => {
      self.refreshAllHighlight()
    })
  },
  ready () {
    this.$nextTick(() => {
      window.hljs.initHighlightingOnLoad()
    })
  }
}
</script>

<template>
  <section id="mostlyUsedSamples">
    <h2 class="section-title">Common Usage</h2>

    <div class="block">
      <h3 class="title"><a class="anchor" id="default">#</a>Default</h3>
      <div class="description">
        <p>Default to 24-hour format <code>HH:mm</code></p>
      </div>
      <div class="codes">
        <pre data-title="HTML"><code v-el:ugdefault class="html">&lt;vue-timepicker&gt;&lt;/vue-timepicker&gt;</code></pre>
      </div>
      <div class="preview">
        <vue-timepicker></vue-timepicker>
      </div>
    </div>

    <div class="block">
      <h3 class="title"><a class="anchor" id="format12hours">#</a>12 Hours</h3>
      <div class="description">
        <p>By properly define the <code>format</code> string, you can set timepicker in form of 12 hours</p>
      </div>
      <div>
        <div class="codes">
<pre data-title="HTML">
<code class="html">&lt;!-- 12-hour sample 1 --&gt;
&lt;vue-timepicker format="hh:mm A"&gt;&lt;/vue-timepicker&gt;

&lt;!-- 12-hour sample 2 --&gt;
&lt;vue-timepicker format="h:m a"&gt;&lt;/vue-timepicker&gt;
</code>
</pre>
        </div>
        <div class="preview">
          <b>12-hour sample 1</b>
          <p>
            <vue-timepicker format="hh:mm A"></vue-timepicker>
          </p>
          <b>12-hour sample 2</b>
          <p>
            <vue-timepicker format="h:m a"></vue-timepicker>
          </p>
        </div>
      </div>
    </div>

    <div class="block">
      <h3 class="title"><a class="anchor" id="seconds">#</a>Seconds Picker</h3>
      <div class="description">
        <p>You can trigger the seconds picker by adding <code>ss</code> or <code>s</code> in your format string.</p>
      </div>
      <div class="codes">
        <pre data-title="HTML"><code class="html">&lt;vue-timepicker format="HH:mm:ss"&gt;&lt;/vue-timepicker&gt;</code></pre>
      </div>
      <div class="preview">
        <vue-timepicker format="HH:mm:ss"></vue-timepicker>
      </div>
    </div>

    <div class="block">
      <h3 class="title"><a class="anchor" id="interval">#</a>Customized Interval</h3>
      <div class="description">
        <p>Timepicker also allows you to display minutes or seconds picker with certain interval, like a 10-minute interval <code>0, 10, 20, ... 50, 60</code> for example</p>
      </div>
      <div class="codes">
<pre data-title="HTML">
<code class="html">&lt;!-- 10-minute interval --&gt;
&lt;vue-timepicker :minute-interval="10"&gt;&lt;/vue-timepicker&gt;

&lt;!-- 15-second interval --&gt;
&lt;vue-timepicker format="HH:mm:ss" :second-interval="15"&gt;&lt;/vue-timepicker&gt;

&lt;!-- 5-minute interval plus 10-second interval --&gt;
&lt;vue-timepicker format="hh:mm:ss" :minute-interval="5" :second-interval="10"&gt;&lt;/vue-timepicker&gt;
</code>
</pre>
      </div>
      <div class="preview">
        <b>10-minute interval</b>
        <p>
          <vue-timepicker :minute-interval="10"></vue-timepicker>
        </p>
        <b>15-second interval</b>
        <p>
          <vue-timepicker format="HH:mm:ss" :second-interval="15"></vue-timepicker>
        </p>
        <b>5-minute interval plus 10-second interval</b>
        <p>
          <vue-timepicker format="hh:mm:ss" :minute-interval="5" :second-interval="10"></vue-timepicker>
        </p>
      </div>
    </div>

    <div class="block">
      <h3 class="title"><a class="anchor" id="sync">#</a>Sync Data</h3>
      <div class="description">
        <p>Manipulate your timepicker's data as usual</p>
      </div>
      <div class="codes">
<pre data-title="JS"><code class="javascript">data: {
  yourFormat: 'hh:mm:ss a',
  yourData: {
    hh: '03',
    mm: '10',
    ss: '00',
    a: 'am'
  }
}</code></pre>
      </div>
      <div class="codes">
        <pre data-title="HTML"><code class="html">&lt;vue-timepicker :format="yourFormat" :time-value.sync="yourData"&gt;&lt;/vue-timepicker&gt;</code></pre>
      </div>
      <div>
        <div class="preview">
          <vue-timepicker :format="yourFormat" :time-value.sync="yourData"></vue-timepicker>
        </div>
      </div>
    </div>

    <div class="block">
      <h3 class="title"><a class="anchor" id="hideClearButton">#</a>Hide Clear Button</h3>
      <div class="description">
        <p>If you don't want to expose the clear button in the UI, <code>hide-clear-button</code> property will do the trick.</p>
      </div>
      <div class="codes">
        <pre data-title="HTML"><code class="html">&lt;vue-timepicker hide-clear-button&gt;&lt;/vue-timepicker&gt;</code></pre>
      </div>
      <div class="preview">
        <vue-timepicker hide-clear-button></vue-timepicker>
      </div>
    </div>

    <div class="block">
      <h3 class="title"><a class="anchor" id="more">#</a>More complex usage</h3>
      <div class="description">
        <p>Didn't find what you need? Please check the <a>Playground</a> or <a href="https://github.com/phoenixwong/vue-timepicker" target="_blank">Documentation</a> for more inspiration.</p>
      </div>
    </div>

  </section>
</template>
