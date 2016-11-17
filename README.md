# Vue Time Picker

A dropdown time picker (hour|minute|second) for **Vue.js (1.x)**, with flexible time format support

> 🎉 **The brand new version for Vue 2.x is up!**
>
> Please check  [vue2-timepicker](https://github.com/phoenixwong/vue2-timepicker) for your Vue 2.x project

## Demo

You can see the `vue-timepicker` in action in the [Demo Page](https://phoenixwong.github.io/vue-timepicker/)

## Dependencies

[Vue.js 1.x](http://v1.vuejs.org/) (>=v1.0.21 <2.0.0)

## Installation

Through NPM (recommended)

```bash
npm install vue-time-picker --save
```

Bower

```bash
bower install vue-timepicker --save
```

## Get Started

**Step 1:** Import VueTimepicker

A. Include with modules

```javascript
// import
import VueTimepicker from 'vue-time-picker'

// Or, require
var VueTimepicker = require('vue-time-picker')

```

B. Include with `<script>` and `<style>`

Just put the `vue-timepicker.min.js` (or `vue-timepicker.js`) script block after Vue itself

```javascript
// Don't forget to call this
Vue.use(window.VueTimepicker)
```

**Step 2**: Include VueTimepicker in your component

```javascript
var yourComponent = new Vue({
  components: {VueTimepicker},
  ...
})
```

**Step 3**: Then, you can introduce the `vue-timepicker` tag anywhere you like in your component's template

```html
<vue-timepicker></vue-timepicker>
```

## Usage

### Basic Usage

```html
<!-- Default to 24-Hour format HH:mm -->
<vue-timepicker></vue-timepicker>
```

### Customized Time Format

```html
<!-- Show seconds picker -->
<vue-timepicker format="HH:mm:ss"></vue-timepicker>

<!-- 12-hour format, with AM/PM picker -->
<vue-timepicker format="hh:mm A"></vue-timepicker>

<!-- 12-hour format, with seconds picker and am/pm picker -->
<vue-timepicker format="hh:mm:ss a"></vue-timepicker>
```

VueTimepicker will recognizes the following tokens in the format string

Section    | Token | Output
---------- | ----- | ---------------
**AM/PM**  | A     | AM PM
           | a     | am pm
**Hour**   | H     | 0 1 ... 22 23
           | HH    | 00 01 ... 22 23
           | h     | 1 2 ... 11 12
           | hh    | 01 02 ... 11 12
           | k     | 1 2 ... 23 24
           | kk    | 01 02 ... 23 24
**Minute** | m     | 0 1 ... 58 59
           | mm    | 00 01 ... 58 59
**Second** | s     | 0 1 ... 58 59
           | ss    | 00 01 ... 58 59

> If not set, `format` string will be default to "HH:mm"

### Customized Picker interval

```html
<!-- Show minute picker's value in the form of 0, 5, 10, ... 55, 60 -->
<vue-timepicker :minute-interval="5"></vue-timepicker>

<!-- Show second picker's value in the form of 0, 10, 20, ... 50, 60 -->
<vue-timepicker :second-interval="10"></vue-timepicker>

<!-- Bind interval config with your own data variable -->
<vue-timepicker :minute-interval="yourMinuteInterval"></vue-timepicker>
```

**Note:** Please do remember to add the `:` or `v-bind:` sign before the interval properties

### Hide Clear Button

```html
<vue-timepicker hide-clear-button></vue-timepicker>
```

### Initalise Time Picker Value

```javascript
// e.g. If you want to assign "10:05:00" as the initial value of vue-timepicker
var yourComponent = new Vue({
  components: {VueTimepicker},
  data: function () {
    return {
      yourTimeValue: {
        HH: "10",
        mm: "05",
        ss: "00"
      },
      ...
    }
  },
  ...
})
```

```html
<!-- HTML -->
<vue-timepicker :time-value.sync="yourTimeValue" format="HH:mm:ss"></vue-timepicker>
```

### Get Time Picker's Current Value

**Method 1:** Read the two-way synced `time-value` variable

```html
<!-- In the last section, we've set the initial value (yourTimeValue) to "10:05:00" -->
<vue-timepicker :time-value.sync="yourTimeValue" format="HH:mm:ss"></vue-timepicker>
```

```javascript
// Then, open the dropdown picker and pick a new time.
// Like setting to "14:30:15" for example
// Check the value after that
console.log(this.yourTimeValue)
// outputs -> {HH: "14", mm: "30", ss: "15"}
```

**Method 2:** Listen to the `vue-timepicker-update` event

```javascript
// 1) Use `events`
var yourComponent = new Vue({
  components: {VueTimepicker},
  events: {
    'vue-timepicker-update': function (eventData) {
      // `eventData` includes the current value of vue-timepicker
      // Add your handler here
    },
    ...
  },
  ...
})

// Or, 2) Use `$on`
this.$on('vue-timepicker-update', function (eventData) {
  // `eventData` includes the current value of vue-timepicker
  // Your handler here
})
```

Unlike the sync `time-value`, which only returns tokens you provided in the initial data (`HH`, `mm` and `ss` in the above case), the `vue-timepicker-update` event will return **all** time format.

In the example above, when picker is set to "14:30:15" in HH:mm:ss format, `vue-timepicker-update` will return the following data:

```javascript
// `vue-timepicker-update` event data
{
  HH: "14",
  H: "14",
  hh: "14",
  a: "am",
  A: "AM",
  h: "14",
  kk: "14",
  k: "14",
  m: "30",
  mm: "30",
  s: "15",
  ss: "15"
}
```

Whereas the `time-value` will only return data with your predefined tokens

```javascript
// Previously defined tokens in `yourTimeValue` are `HH`, `mm` and `ss`
// Hence, `time-value`'s synced data returns:
{
  HH: "14",
  mm: "30",
  ss: "15"
}
```

**Method 3:** Add `@change` event handler

```html
<!-- A: No argument -->
<vue-timepicker :time-value.sync="yourTimeValue" @change="changeHandler"></vue-timepicker>

<!-- B: Custom arguments -->
<vue-timepicker :time-value.sync="yourTimeValue" @change="otherChangeHandler($arguments, 'foo', 'bar')"></vue-timepicker>
```

```javascript
// A: No argument
changeHandler (eventData) {
  console.log(eventData)
  // -> {data: {HH:..., mm:... }}
}

// B: Custom arguments
otherChangeHandler (eventData, yourArg1, yourArg2) {
  console.log(eventData)
  // -> [{data: {HH:..., mm:... }}]
  console.log(yourArg1)
  // -> 'foo'
  console.log(yourArg2)
  // -> 'bar'
}
```

## Props API

Prop                  | Type      | Required | Default Value
--------------------- | --------- | -------- | -------------
**format**            | _String_  | no       | "HH:mm"
**minute-interval**   | _Number_  | no       | _undefined_
**second-interval**   | _Number_  | no       | _undefined_
**time-value**        | _Object_  | no       | _undefined_
**hide-clear-button** | _Boolean_ | no       | false

## Contribution

Please feel free to fork and help developing.

```bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev
```

For detailed explanation on how things work, checkout the [webpack guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).

## Change Log

Detail changes for each release are documented in  [CHANGELOG.md](https://github.com/phoenixwong/vue-timepicker/blob/master/CHANGELOG.md)

## License

[MIT](http://opensource.org/licenses/MIT)
