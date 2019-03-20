# A slider component based on Vue
## Table of contents
- [Installation](#installation)
- [Import](#import)
- [Usage](#usage)
- [Available props](#available-props)
- [events](#events)
## Installation
```bash
npm install rgg-slider
```
## Import
```js
import slider from 'rgg-slider'

export default {
    // ...
    components: {
        slider,
    },
    // ...
}
```
## usage
rgg-slider is a slot-based component, to provide maximum flexibility.
Since every ounce of html is created by a consumer (ie. you), you can customize every piece of the component as you wish.
### Very simple usage, without any CSS defined
What you need to do is provide your own slider item.
```html
<slider
    :position="pindex"
    :list="list"
    @slide="slide($event)">
    <template v-for="(item, index) in list">
        <div :slot="`item${index}`"></div>
    </template>
</slider>
```
Tip: It's very important that the slot of every single item must be named as `item${index}`.
## available-props
- `list` _necessary_  - The list of your slider items. The component will show nothing if you do not pass that.
- `containerStyle` _optional_ - The style of a single item. You can pass any css style to that to personalize style of a single item. 
- `position` _optional_ - You can decide which item shows first when the slider show up. Example if you pass 3 to that prop, the 4th item will show first. The default value is _0_.
- `play` _optional_ - The slider can play automatically. Default value: _false_.
- `duration` _optional_ - You can decide the duration of a single sliding animation. If the value of _play_ is false this prop will do nothing. Default value: _3000_.
## events
- `slide` - Once a sliding animation finished this method will invoke to the parent component. There is a param passed to the method, which decalres the direction of this animation.

Example: 
```js
slide(data) {
    if (data.target === 1) { // slide forward
        // ...
    } else if (data.target === -1) { // slide backward
        // ...
    }
},
```
