<script>
import VueTimepicker from 'src/vue-timepicker.vue'

var scrollHandler

export default {
  name: 'Playground',
  components: [VueTimepicker],
  data () {
    return {
      tokens: {
        hour: ['HH', 'H', 'hh', 'h', 'kk', 'k'],
        minute: ['mm', 'm'],
        second: ['ss', 's', 'none'],
        apm: ['A', 'a']
      },

      type: {
        hour: 'hh',
        minute: 'mm',
        second: 'ss',
        apm: 'A'
      },

      selected: {
        hour: 1,
        minute: 0,
        second: 0,
        apm: 'am'
      },

      hourRange: {
        min: 1,
        max: 12
      },

      interval: {
        minute: 1,
        second: 1
      },

      customInterval: {
        minute: false,
        second: false
      },

      enableClearBtn: true,

      playgroundData: {},

      playgroundFullValue: {},

      muteWatch: false,

      scrollTop: 0
    }
  },

  computed: {
    formatString () {
      let formatString = this.type.hour + ':' + this.type.minute
      if (this.type.second !== 'none') {
        formatString += (':' + this.type.second)
      }
      if (this.type.apm) {
        formatString += (' ' + this.type.apm)
      }
      return formatString
    },

    needApm () {
      return this.type.hour === 'h' || this.type.hour === 'hh'
    },

    htmlCode () {
      let start = '<vue-timepicker'
      let end = '>\n</vue-timepicker>'

      start += (' format="' + this.formatString + '"')

      if (this.customInterval.minute) {
        start += ('\n :minute-interval="' + this.interval.minute + '"')
      }

      if (this.customInterval.second) {
        start += ('\n :second-interval="' + this.interval.second + '"')
      }

      if (!this.enableClearBtn) {
        start += ('\n hide-clear-button')
      }

      return start + end
    },

    htmlCodeWithVar () {
      let htmlCode = this.htmlCode
      const end = '>\n</vue-timepicker>'
      const newEnd = '\n :time-value.sync="yourTimeValue">\n</vue-timepicker>'
      htmlCode = htmlCode.replace(end, newEnd)

      this.refreshHighlight('pghtml')
      this.refreshHighlight('pghtmlvar')

      return htmlCode
    },

    yourTimeValue () {
      let code = 'data: function () {\n'
      code += '  return {\n'
      code += '    yourTimeValue: {\n'
      code += this.listTimeValue()
      code += '\n    }\n'
      code += '  }\n'
      code += '}'
      return code
    }
  },

  watch: {
    'selected': {
      deep: true,
      handler: 'updatePlaygroundData'
    },

    'type': {
      deep: true,
      handler: 'updatePlaygroundData'
    },

    needApm (isNeeded) {
      if (isNeeded) {
        if (!this.selected.apm) {
          this.type.apm = 'A'
          this.selected.apm = 'am'
        }
      } else {
        this.type.apm = ''
        this.selected.apm = ''
      }
    },

    'customInterval': {
      deep: true,
      handler: function (custom) {
        if (!custom.minute) {
          this.interval.minute = 1
        }
        if (!custom.second) {
          this.interval.second = 1
        }
      }
    }
  },

  methods: {
    updateHourRange (hourType) {
      hourType = hourType || this.type.hour
      let hourRange = {}
      switch (hourType) {
        case 'h':
        case 'hh':
          hourRange.min = 1
          hourRange.max = 12
          break
        case 'H':
        case 'HH':
          hourRange.min = 0
          hourRange.max = 23
          break
        case 'k':
        case 'kk':
          hourRange.min = 1
          hourRange.max = 24
          break
      }
      this.hourRange = hourRange
      this.handleOutRangeData()
    },

    handleOutRangeData () {
      if (this.selected.hour > this.hourRange.max) {
        this.selected.hour = this.hourRange.max
      } else if (this.selected.hour < this.hourRange.min) {
        this.selected.hour = this.hourRange.min
      }
    },

    formatValue (type, value) {
      switch (type) {
        case 'H':
        case 'h':
        case 'k':
        case 'm':
        case 's':
          return String(value)
        case 'HH':
        case 'hh':
        case 'kk':
        case 'mm':
        case 'ss':
          return value < 10 ? `0${value}` : String(value)
        default:
          return ''
      }
    },

    updatePlaygroundData () {
      if (this.muteWatch) { return }
      let data = {}
      data[this.type.hour] = this.formatValue(this.type.hour, this.selected.hour)
      data[this.type.minute] = this.formatValue(this.type.minute, this.selected.minute)

      if (this.type.second !== 'none') {
        data[this.type.second] = this.formatValue(this.type.second, this.selected.second)
      }

      if (this.type.apm) {
        data[this.type.apm] = this.type.apm === 'A' ? (this.selected.apm).toUpperCase() : this.selected.apm
      }

      this.playgroundData = data
      this.refreshHighlight('pgvalue')
      this.refreshHighlight('pgdata')
    },

    listTimeValue () {
      const data = this.playgroundData
      const len = Object.keys(data).length
      let string = ''
      Object.keys(data).forEach((k, i) => {
        string += ('      ' + k + ': ' + '"' + data[k] + '"')
        if (i < len - 1) {
          string += '\n'
        }
      })
      return string
    },

    updateRangeValue (data) {
      this.muteWatch = true
      this.selected.hour = Number(data[this.type.hour])
      this.selected.minute = Number(data[this.type.minute])
      this.selected.second = Number(data[this.type.second])
      this.selected.apm = (data[this.type.apm] || '').toLowerCase()
      this.refreshHighlight('pgdata')
      this.muteWatch = false
    },

    refreshHighlight (elm) {
      const self = this
      this.$nextTick(() => {
        window.hljs.highlightBlock(self.$els[elm])
      })
    },

    asideStyle () {
      const initPaddingTop = 220
      const minPaddingTop = 50
      return {paddingTop: (Math.max(initPaddingTop - this.scrollTop, minPaddingTop)) + 'px'}
    }
  },

  events: {
    'vue-timepicker-update': function (data) {
      this.playgroundFullValue = data
      this.updateRangeValue(data)
      this.refreshHighlight('pgvalue')
    }
  },

  attached () {
    const self = this
    this.$nextTick(() => {
      self.updateHourRange()
      self.updatePlaygroundData()
      window.hljs.initHighlightingOnLoad()
    })
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
<section id="playground">
  <main>
    <h2 class="section-title">Timepicker Playground</h2>
    <div id="configPanel">
      <div class="config-block" id="typesSelection">
        <h3 class="subtitle"><a class="anchor">#</a>Generate <code>format</code> string</h3>
        <div class="config-row">
          <label class="row-label">Hour:</label>
          <label class="options" v-for="htype in tokens.hour" :for="'hour_type' + $index">
            <input v-model="type.hour" @click="updateHourRange(htype)" :value="htype" :id="'hour_type' + $index" type="radio" name="hour_type" /> {{htype}}
          </label>
        </div>
        <div class="config-row">
          <label class="row-label">Minute:</label>
          <label class="options" v-for="mtype in tokens.minute" :for="'minute_type' + $index">
            <input v-model="type.minute" :value="mtype" :id="'minute_type' + $index" type="radio" name="minute_type" /> {{mtype}}
          </label>
        </div>
        <div class="config-row">
          <label class="row-label">Second:</label>
          <label class="options" v-for="stype in tokens.second" :for="'second_type' + $index">
            <input v-model="type.second" :value="stype" :id="'second_type' + $index" type="radio" name="second_type" /> {{stype}}
          </label>
        </div>
        <div class="config-row" v-if="needApm">
          <label class="row-label">AM/PM:</label>
          <label class="options" v-for="atype in tokens.apm" :for="'apm_type' + $index">
            <input v-model="type.apm" :value="atype" :id="'apm_type' + $index" type="radio" name="apm_type" /> {{atype}}
          </label>
        </div>
      </div>

      <div class="config-block">
        <h3 class="subtitle"><a class="anchor">#</a>Customized Interval</h3>
        <div class="config-row group">
          <label>
            <input v-model="customInterval.minute" type="checkbox" /> Minute Interval
          </label>
          <label v-if="customInterval.minute" class="range-wrapper">
            <input v-model="interval.minute" type="range" min="0" max="60" step="1" />
            <span v-text="interval.minute"></span>
          </label>
        </div>
        <div class="config-row group">
          <label>
            <input v-model="customInterval.second" type="checkbox" /> Second Interval
          </label>
          <label v-if="customInterval.second" class="range-wrapper" >
            <input v-model="interval.second" type="range" min="0" max="60" step="1" />
            <span v-text="interval.second"></span>
          </label>
        </div>
      </div>

      <div class="config-block">
        <h3 class="subtitle"><a class="anchor">#</a>Clear Button</h3>
        <div class="config-row group">
          <label for="enable_btn_true">
            <input v-model="enableClearBtn" type="radio" id="enable_btn_true" name="enable_btn" :value="true" /> Enable
          </label>
          <label for="enable_btn_false">
            <input v-model="enableClearBtn" type="radio" id="enable_btn_false" name="enable_btn" :value="false" /> Disable
          </label>
        </div>
      </div>

      <div class="codes">
        <pre data-title="HTML"><code v-el:pghtml class="html" v-text="htmlCode"></code></pre>
      </div>

      <div class="config-block" id="valuesSelection">
        <h3 class="subtitle"><a class="anchor">#</a>Bind <code>time-value</code> data</h3>
        <div class="config-row">
          <label class="row-label">Hour:</label>
          <label class="options">
            <input v-model="selected.hour" type="range" :min="hourRange.min" :max="hourRange.max" step="1" />
            <span v-text="selected.hour"></span>
          </label>
        </div>
        <div class="config-row">
          <label class="row-label">Minute:</label>
          <label class="options">
            <input v-model="selected.minute" type="range" min="0" max="59" :step="interval.minute" />
            <span v-text="selected.minute"></span>
          </label>
        </div>
        <div v-if="type.second !== 'none'" class="config-row">
          <label class="row-label">Second:</label>
          <label class="options">
            <input v-model="selected.second" type="range" min="0" max="59" :step="interval.second" />
            <span v-text="selected.second"></span>
          </label>
        </div>
        <div v-if="needApm" :class="{capitailized: type.apm === 'A'}" class="config-row">
          <label class="row-label">AM/PM:</label>
          <label class="options" for="selected_apm_am">
            <input v-model="selected.apm" type="radio" id="selected_apm_am" name="selected_apm" value="am" /> am
          </label>
          <label class="options" for="selected_apm_pm">
            <input v-model="selected.apm" type="radio" id="selected_apm_pm" name="selected_apm" value="pm" /> pm
          </label>
        </div>
      </div>

      <div class="codes">
        <pre data-title="JS"><code v-el:pgdata class="javascript" v-text="yourTimeValue"></code></pre>
      </div>

      <div class="codes">
        <pre data-title="HTML"><code v-el:pghtmlvar class="html" v-text="htmlCodeWithVar"></code></pre>
      </div>

    </div>

  </main>

  <aside class="previews" :style="asideStyle()">
    <div id="playgroundPreview" class="preview">
      <b>Format string: </b>
      <span v-text="formatString"></span>
      <p>
        <vue-timepicker :format="formatString" :time-value.sync="playgroundData" :minute-interval="interval.minute" :second-interval="interval.second" :hide-clear-button="!enableClearBtn"></vue-timepicker>
      </p>
    </div>

    <div id="dispatchedValue" class="codes">
      <pre data-title="vue-timepicker-update"><code v-el:pgvalue class="json" v-text="playgroundFullValue | json 2"></code></pre>
    </div>
  </aside>

</section>
</template>
