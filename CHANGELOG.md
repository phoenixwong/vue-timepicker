# CHANGELOG

> The Change Log of Vue Time Picker `vue-timepicker`

## v 0.2.6

### Improvements

- Added stop propagation `.stop` to click events
- Add support to `<label for="...">`

```html
<!-- Sample -->
<label for="yourID">Your Label Text</label>
<vue-timepicker id="yourID"></vue-timepicker>
```

## v 0.2.5

### Update

Introduce a brand new version for Vue 2.x: [vue2-timepicker](https://github.com/phoenixwong/vue2-timepicker)

### Improvements

- Reduced the times of direct manipulation of watched/synced variables, for better performance.
- Improved the returning data of `vue-timepicker-update` event in some situation, like dynamically changing the format string for example.

## v 0.2.3

### Fixes

Fixes Demo page code block and scrolling issue in some Webkit browsers (Safari and some
previous version of Chrome) and Firefox

## v 0.2.2

### New

Added sample usage of `v-for` and `@change` in [Demo Page](https://phoenixwong.github.io/vue-timepicker/)

### Improvements

- Enhanced `@change` event. Return full values when `time-value` is not set
- When data field is empty, return empty string `''` other than `0` or `00` as placeholder in `vue-timepicker-update` event

### Fixes

Fixes `second-interval` and `minute-interval`'s "undefined" bug under ES2015

## v 0.2.1

### New

Introduce `@change` event

### Improvements

Better support `v-for` usage

### Fixes

Fixes for `:time-value`'s binding data not being updated immediately

## v 0.2.0

### New

The ES5 solution is available!

### Fixes

Minor style and format fixes:

- Enhanced clear button, with increased clickable zone and better vertical alignment
- CSS styles are now provided in a separate file

## v 0.1.2

The first notable release of vue-timepicker
