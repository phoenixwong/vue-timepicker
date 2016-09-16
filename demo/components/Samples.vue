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
      yourFormat: 'hh:mm:ss a',
      yourDaysArray: [
        {start_time: {HH: '08', mm: '00'}, end_time: {HH: '09', mm: '00'}},
        {start_time: {HH: '15', mm: '00'}, end_time: {HH: '', mm: ''}},
        {start_time: {HH: '', mm: ''}, end_time: {HH: '13', mm: '30'}},
        {start_time: {HH: '', mm: ''}, end_time: {HH: '', mm: ''}}
      ],
      muteFlowListener: true,
      latestDataFlow: undefined,
      demoData1: {HH: '08', mm: '30'},
      demoData2: {HH: '10', mm: '45'},
      demoArgs: undefined
    }
  },
  events: {
    'vue-timepicker-update': function () {
      const self = this
      this.$nextTick(() => {
        self.refreshAllHighlight()
      })
    }
  },
  methods: {
    refreshHighlightNextTick () {
      const self = this
      this.$nextTick(() => {
        self.refreshAllHighlight()
      })
    },

    refreshAllHighlight () {
      if (!this.$el) { return }
      const codeBlocks = this.$el.querySelectorAll('pre code')
      Array.prototype.forEach.call(codeBlocks, (block) => {
        window.hljs.highlightBlock(block)
      })
    },

    changeHandler (eventData) {
      if (this.muteFlowListener) { return }
      this.latestDataFlow = eventData
      this.demoArgs = undefined
      this.refreshHighlightNextTick()
    },

    otherChangeHandler (eventData, arg1, arg2) {
      if (this.muteFlowListener) { return }
      this.latestDataFlow = eventData
      this.demoArgs = {
        arg1: arg1,
        arg2: arg2
      }
      this.refreshHighlightNextTick()
    }
  },
  compiled () {
    this.refreshHighlightNextTick()
    const self = this
    window.setTimeout(() => {
      self.muteFlowListener = false
    }, 1000)
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
      <h3 class="title"><a class="anchor" id="vForSample">#</a>Work with <code>v-for</code></h3>
      <div class="description">
        <p>Here's a quick sample of <code>v-for</code> usage</p>
      </div>
      <div class="codes">
<pre data-title="JS"><code class="javascript">data: {
  yourDaysArray: [
    {start_time: {HH: '08', mm: '00'}, end_time: {HH: '09', mm: '00'}},
    {start_time: {HH: '15', mm: '00'}, end_time: {HH: '', mm: ''}},
    {start_time: {HH: '', mm: ''}, end_time: {HH: '13', mm: '30'}},
    {start_time: {HH: '', mm: ''}, end_time: {HH: '', mm: ''}}
  ]
}</code></pre>
      </div>
      <div class="codes">
<pre data-title="HTML"><code class="html">
&lt;p v-for="day in yourDaysArray"&gt;
  &lt;label&gt;Day <span>{{</span> $index + 1 <span>}}</span>: &lt;/label&gt;
  &lt;vue-timepicker :time-value.sync="day.start_time"&gt;&lt;/vue-timepicker&gt;
  &lt;span&gt; to &lt;/span&gt;
  &lt;vue-timepicker :time-value.sync="day.end_time"&gt;&lt;/vue-timepicker&gt;
&lt;/p&gt;
</code></pre>
      </div>
      <div class="preview">
        <p v-for="day in yourDaysArray">
          <label>Day {{ $index + 1 }}: </label>
          <vue-timepicker :time-value.sync="day.start_time"></vue-timepicker>
          <span> to </span>
          <vue-timepicker :time-value.sync="day.end_time"></vue-timepicker>
        </p>
      </div>
      <div class="codes">
<pre data-title="yourDaysArray JSON"><code class="json" v-text="yourDaysArray | json 2">
</code></pre>
      </div>
    </div>

    <div class="block">
      <h3 class="title"><a class="anchor" id="onChangeSample">#</a>The <code>change</code> Event</h3>
      <div class="description">
        <p>The <code>change</code> event was introduced in v0.2.1. You can use <code>@change</code> to target and handle individual return data.</p>
      </div>

      <div class="codes">
<pre data-title="JS"><code class="javascript">
methods: {
  // No argument
  changeHandler (eventData) {
    // eventData -> {data: {HH:..., mm:...}}
  },

  // Customized arguments
  otherChangeHandler (eventData, arg1, arg2) {
    // eventData -> [{data: {HH:..., mm:...}}]
    // arg1 -> 'foo' or 'bar' in this demo
    // arg2 -> 1 or 42 in this demo
  }
}
</code></pre>
      </div>

      <div class="codes">
<pre data-title="HTML"><code class="html">&lt;!--
[A] With `time-value` set
--&gt;

&lt;!-- A-1: No argument --&gt;
&lt;vue-timepicker :time-value.sync="demoData1" @change="changeHandler"&gt;&lt;/vue-timepicker&gt;

&lt;!-- A-2: Custom argument --&gt;
&lt;vue-timepicker :time-value.sync="demoData2" @change="otherChangeHandler($arguments, 'foo', 1)"&gt;&lt;/vue-timepicker&gt;

&lt;!--
[B] No binding `time-value`
--&gt;

&lt;!-- B-1: No argument --&gt;
&lt;vue-timepicker @change="changeHandler"&gt;&lt;/vue-timepicker&gt;

&lt;!-- B-2: Custom argument --&gt;
&lt;vue-timepicker @change="otherChangeHandler($arguments, 'bar', 42)"&gt;&lt;/vue-timepicker&gt;
</code></pre>
      </div>

      <div class="preview">
        <p>
          <b>[A] With `time-value` set, <code>change</code> event will only returns values in predefined format</b>
        </p>

        <b>A-1: No argument</b>
        <p>
          <vue-timepicker :time-value.sync="demoData1" @change="changeHandler"></vue-timepicker>
        </p>

        <b>A-2: Custom argument ('foo', 1)</b>
        <p>
          <vue-timepicker :time-value.sync="demoData2" @change="otherChangeHandler($arguments, 'foo', 1)"></vue-timepicker>
        </p>

        <p>
          <b>[B] No binding `time-value`, full value data will be returned</b>
        </p>

        <b>B-1: No argument</b>
        <p>
          <vue-timepicker @change="changeHandler"></vue-timepicker>
        </p>

        <b>B-2: Custom argument ('bar', 42)</b>
        <p>
          <vue-timepicker @change="otherChangeHandler($arguments, 'bar', 42)"></vue-timepicker>
        </p>

      </div>

      <div class="codes" v-if="demoArgs">
<pre data-title="Custom Arguments"><code class="javascript" v-text="demoArgs | json">
</code></pre>
      </div>

      <div class="codes" v-if="latestDataFlow">
<pre data-title="Latest eventData"><code class="javascript" v-text="latestDataFlow | json">
</code></pre>
      </div>

    </div>

    <div class="block footer-links">
      <slot name="footer-links"></slot>
    </div>

  </section>
</template>
