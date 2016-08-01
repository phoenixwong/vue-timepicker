<script type="text/ecmascript-6">
const CONFIG = {
  HOUR_TOKENS: ['HH', 'H', 'hh', 'h', 'kk', 'k'],
  MINUTE_TOKENS: ['mm', 'm'],
  SECOND_TOKENS: ['ss', 's'],
  APM_TOKENS: ['A', 'a']
}

export default {
  name: 'VueTimepicker',

  props: {
    timeValue: {type: Object, twoWay: true},
    hideClearButton: {type: Boolean},
    format: {type: String},
    minuteInterval: {type: Number},
    secondInterval: {type: Number}
  },

  data () {
    return {
      hours: [],
      minutes: [],
      seconds: [],
      apms: [],
      showDropdown: false,
      hourType: 'HH',
      minuteType: 'mm',
      secondType: '',
      apmType: '',
      hour: '',
      minute: '',
      second: '',
      apm: '',
      fullValues: undefined
    }
  },

  computed: {
    displayTime: function () {
      let formatString = (this.format || 'HH:mm') + ''
      if (this.hour) {
        formatString = formatString.replace(new RegExp(this.hourType, 'g'), this.hour)
      }
      if (this.minute) {
        formatString = formatString.replace(new RegExp(this.minuteType, 'g'), this.minute)
      }
      if (this.second && this.secondType) {
        formatString = formatString.replace(new RegExp(this.secondType, 'g'), this.second)
      }
      if (this.apm && this.apmType) {
        formatString = formatString.replace(new RegExp(this.apmType, 'g'), this.apm)
      }
      return formatString
    }
  },

  watch: {
    'format': 'renderFormat',
    'minuteInterval': function (newInteval) {
      this.renderList('minute', newInteval)
    },
    'secondInterval': function (newInteval) {
      this.renderList('second', newInteval)
    },
    'timeValue': 'readValues',
    'displayTime': 'fillValues'
  },

  methods: {
    formatValue (type, i) {
      switch (type) {
        case 'H':
        case 'm':
        case 's':
          return i + ''
        case 'HH':
        case 'mm':
        case 'ss':
          return i < 10 ? `0${i}` : i + ''
        case 'h':
        case 'k':
          return (i + 1) + ''
        case 'hh':
        case 'kk':
          return (i + 1) < 10 ? `0${i + 1}` : (i + 1) + ''
        default:
          return ''
      }
    },

    checkAcceptingType (validValues, formatString, fallbackValue) {
      if (!validValues || !formatString || !formatString.length) { return '' }
      for (let i = 0; i < validValues.length; i++) {
        if (formatString.indexOf(validValues[i]) > -1) {
          return validValues[i]
        }
      }
      return fallbackValue || ''
    },

    renderFormat (newFormat) {
      newFormat = newFormat || this.format
      if (!newFormat || !newFormat.length) {
        newFormat = 'HH:mm'
      }

      this.hourType = this.checkAcceptingType(CONFIG.HOUR_TOKENS, newFormat, 'HH')
      this.minuteType = this.checkAcceptingType(CONFIG.MINUTE_TOKENS, newFormat, 'mm')
      this.secondType = this.checkAcceptingType(CONFIG.SECOND_TOKENS, newFormat)
      this.apmType = this.checkAcceptingType(CONFIG.APM_TOKENS, newFormat)

      this.renderHoursList()
      this.renderList('minute')

      if (this.secondType) {
        this.renderList('second')
      }

      if (this.apmType) {
        this.renderApmList()
      }

      const self = this
      this.$nextTick(() => {
        self.readValues()
      })
    },

    renderHoursList () {
      const hoursCount = (this.hourType === 'h' || this.hourType === 'hh') ? 12 : 24
      this.hours = []
      for (let i = 0; i < hoursCount; i++) {
        this.hours.push(this.formatValue(this.hourType, i))
      }
    },

    renderList (listType, interval) {
      if (listType === 'second') {
        interval = interval || this.secondInterval
      } else if (listType === 'minute') {
        interval = interval || this.minuteInterval
      } else {
        return
      }

      if (interval === 0) {
        interval = 60
      } else if (interval > 60) {
        window.console.warn('`' + listType + '-interval` should be less than 60. Current value is', interval)
        interval = 1
      } else if (interval < 1) {
        window.console.warn('`' + listType + '-interval` should be NO less than 1. Current value is', interval)
        interval = 1
      } else if (!interval) {
        interval = 1
      }

      if (listType === 'minute') {
        this.minutes = []
      } else {
        this.seconds = []
      }

      for (let i = 0; i < 60; i += interval) {
        if (listType === 'minute') {
          this.minutes.push(this.formatValue(this.minuteType, i))
        } else {
          this.seconds.push(this.formatValue(this.secondType, i))
        }
      }
    },

    renderApmList () {
      this.apms = []
      if (!this.apmType) { return }
      this.apms = this.apmType === 'A' ? ['AM', 'PM'] : ['am', 'pm']
    },

    readValues () {
      if (!this.timeValue) { return }

      const values = Object.keys(this.timeValue)

      if (values.indexOf(this.hourType) > -1) {
        this.hour = this.timeValue[this.hourType]
      }

      if (values.indexOf(this.minuteType) > -1) {
        this.minute = this.timeValue[this.minuteType]
      }

      if (values.indexOf(this.secondType) > -1) {
        this.second = this.timeValue[this.secondType]
      }

      if (values.indexOf(this.apmType) > -1) {
        this.apm = this.timeValue[this.apmType]
      }

      this.fillValues()
    },

    fillValues () {
      let fullValues = {}

      const baseHour = this.hour
      const baseHourType = this.hourType

      const hourValue = Number(baseHour)
      const baseOnTwelveHours = this.isTwelveHours(baseHourType)
      const apmValue = this.apm ? String(this.apm).toLowerCase() : false

      CONFIG.HOUR_TOKENS.forEach((token) => {
        if (token === baseHourType) {
          fullValues[token] = baseHour
          return
        }

        let value
        let apm
        switch (token) {
          case 'H':
          case 'HH':
            if (baseOnTwelveHours) {
              if (apmValue === 'pm') {
                value = hourValue < 12 ? hourValue + 12 : hourValue
              } else {
                value = hourValue % 12
              }
            } else {
              value = hourValue % 24
            }
            fullValues[token] = (token === 'HH' && value < 10) ? `0${value}` : String(value)
            break
          case 'k':
          case 'kk':
            if (baseOnTwelveHours) {
              if (apmValue === 'pm') {
                value = hourValue < 12 ? hourValue + 12 : hourValue
              } else {
                value = hourValue === 12 ? 24 : hourValue
              }
            } else {
              value = hourValue === 0 ? 24 : hourValue
            }
            fullValues[token] = (token === 'kk' && value < 10) ? `0${value}` : String(value)
            break
          case 'h':
          case 'hh':
            if (apmValue) {
              value = hourValue
              apm = apmValue || 'am'
            } else {
              if (hourValue > 11) {
                apm = 'pm'
                value = hourValue === 12 ? 12 : hourValue % 12
              } else {
                apm = 'am'
                value = hourValue % 12 === 0 ? 12 : hourValue
              }
            }
            fullValues[token] = (token === 'hh' && value < 10) ? `0${value}` : String(value)
            fullValues.a = apm
            fullValues.A = apm.toUpperCase()
            break
        }
      })

      const minuteValue = Number(this.minute)
      fullValues.m = String(minuteValue)
      fullValues.mm = minuteValue < 10 ? `0${minuteValue}` : String(minuteValue)

      const secondValue = Number(this.second) || 0
      fullValues.s = String(secondValue)
      fullValues.ss = secondValue < 10 ? `0${secondValue}` : String(secondValue)

      this.fullValues = fullValues

      this.$dispatch('vue-timepicker-update', fullValues)
    },

    isTwelveHours (token) {
      return token === 'h' || token === 'hh'
    },

    toggleDropdown () {
      this.showDropdown = !this.showDropdown
    },

    select (type, value) {
      if (type === 'hour') {
        this.hour = value
      } else if (type === 'minute') {
        this.minute = value
      } else if (type === 'second') {
        this.second = value
      } else if (type === 'apm') {
        this.apm = value
      }
    },

    showClearBtn () {
      if ((this.hour && this.hour !== '') || (this.minute && this.minute !== '')) {
        return true
      } else {
        return false
      }
    },

    clearTime () {
      this.hour = ''
      this.minute = ''
      this.second = ''
      this.apm = ''
    }
  },

  ready () {
    this.renderFormat()
  }
}
</script>

<template>
<span class="time-picker">
  <input class="display-time" v-model="displayTime" @click="toggleDropdown" type="text" readonly />
  <span class="clear-btn" v-if="!hideClearButton" v-show="!showDropdown && showClearBtn()" @click="clearTime"><i>&times;</i></span>
  <div class="time-picker-overlay" v-if="showDropdown" @click="toggleDropdown"></div>
  <div class="dropdown" v-show="showDropdown">
    <div class="select-list">
      <ul class="hours">
        <li class="hint" v-text="hourType"></li>
        <li v-for="hr in hours" v-text="hr" :class="{active: hour === hr}" @click="select('hour', hr)"></li>
      </ul>
      <ul class="minutes">
        <li class="hint" v-text="minuteType"></li>
        <li v-for="m in minutes" v-text="m" :class="{active: minute === m}" @click="select('minute', m)"></li>
      </ul>
      <ul class="seconds" v-if="secondType">
        <li class="hint" v-text="secondType"></li>
        <li v-for="s in seconds" v-text="s" :class="{active: second === s}" @click="select('second', s)"></li>
      </ul>
      <ul class="apms" v-if="apmType">
        <li class="hint" v-text="apmType"></li>
        <li v-for="a in apms" v-text="a" :class="{active: apm === a}" @click="select('apm', a)"></li>
      </ul>
    </div>
  </div>
</span>
</template>

<style>
.time-picker {
  display: inline-block;
  position: relative;
  font-size: 1em;
  width: 10em;
  font-family: sans-serif;
}

.time-picker * {
  box-sizing: border-box;
}

.time-picker input.display-time {
  border: 1px solid #d2d2d2;
  width: 10em;
  height: 2.2em;
  padding: 0.3em 0.5em;
  font-size: 1em;
}

.time-picker .clear-btn {
  position: absolute;
  display: inline-block;
  top: 0.6em;
  right: 0.3em;
  z-index: 3;
  font-size: 1em;
  height: 1em;
  width: 1em;
  border-radius: 1em;
  color: #fff;
  background: #d2d2d2;

  -webkit-transition: background .2s;
  transition: background .2s;
}

.time-picker .clear-btn:hover {
  background: #797979;
  cursor: pointer;
}

.time-picker .clear-btn i {
  font-style: normal;
  position: relative;
  right: -0.18em;
  top: -0.07em;
}

.time-picker .time-picker-overlay {
  z-index: 2;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
}

.time-picker .dropdown {
  position: absolute;
  z-index: 5;
  top: calc(2.2em + 2px);
  left: 0;
  background: #fff;
  box-shadow: 0 1px 6px rgba(0,0,0,0.15);
  width: 10em;
  height: 10em;
  font-weight: normal;
}

.time-picker .dropdown .select-list {
  width: 10em;
  height: 10em;
  overflow: hidden;
  display: flex;
  flex-flow: row nowrap;
  align-items: stretch;
  justify-content: space-between;
}

.time-picker .dropdown ul {
  padding: 0;
  margin: 0;
  list-style: none;

  flex: 1;
  overflow-x: hidden;
  overflow-y: auto;
}

.time-picker .dropdown ul.minutes,
.time-picker .dropdown ul.seconds,
.time-picker .dropdown ul.apms{
  border-left: 1px solid #fff;
}

.time-picker .dropdown ul li {
  text-align: center;
  padding: 0.3em 0;
  color: #161616;
}

.time-picker .dropdown ul li:not(.hint):hover {
  background: rgba(0,0,0,.08);
  color: #161616;
  cursor: pointer;
}

.time-picker .dropdown ul li.active,
.time-picker .dropdown ul li.active:hover {
  background: #41B883;
  color: #fff;
}

.time-picker .dropdown .hint {
  color: #a5a5a5;
  cursor: default;
  font-size: 0.8em;
}
</style>
