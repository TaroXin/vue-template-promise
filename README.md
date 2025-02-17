# vue-template-promise

[![NPM version](https://img.shields.io/npm/v/vue-template-promise?color=a1b858&label=)](https://www.npmjs.com/package/vue-template-promise)

Template as Promise in Vue. Useful for constructing custom Dialogs, Modals, Toasts, etc.

[**Why?**](#why)

```html
<script setup lang="ts">
import { useTemplatePromise } from 'vue-template-promise'

const TemplatePromise = useTemplatePromise<ReturnType>()

async function open() {
  const result = await TemplatePromise.start()
  // button is clicked, result is 'ok'
}
</script>

<template>
  <TemplatePromise v-slot="{ promise, resolve, reject, args }">
    <!-- your UI -->
    <button @click="resolve('ok')">OK</button>
  </TemplatePromise>
</template>
```

## Features

- 👨‍💻 **Programmatic** - call your UI as a promise
- 🧩 **Template** - use Vue template to render, not a new DSL
- 🦾 **TypeScript** - full type safety via generic type
- ⚪️ **Renderless** - you take full control of the UI
- 🚀 **Lightweight** - only <400B gzipped
- 🎨 **Transition** - use support Vue transition

## Install

```bash
npm i vue-template-promise
```

## Usage

`useTemplatePromise` returns a **Vue Component** that you can directly use in your template with `<script setup>`

```ts
import { useTemplatePromise } from 'vue-template-promise'

const TemplatePromise = useTemplatePromise()
const MyPromise = useTemplatePromise<boolean>() // with generic type
```

In template, use `v-slot` to access the promise and resolve functions.

```html
<template>
  <TemplatePromise v-slot="{ promise, resolve, reject, args }">
    <!-- you can have anything -->
    <button @click="resolve('ok')">OK</button>
  </TemplatePromise>
  <MyPromise v-slot="{ promise, resolve, reject, args }">
    <!-- another one -->
  </MyPromise>
</template>
```

The slot will not be rendered initially (similar to `v-if="false"`), until you call the `start` method from the component.

```ts
const result = await TemplatePromise.start()
```

Once `resolve` or `reject` is called in the template, the promise will be resolved or rejected, returning the value you passed in. Once resolved, the slot will be removed automatically.

### Passing Arguments

You can pass arguments to the `start` with arguments.

```ts
import { useTemplatePromise } from 'vue-template-promise'

const TemplatePromise = useTemplatePromise<boolean, [string, number]>()
```

```ts
const result = await TemplatePromise.start('hello', 123) // Pr
```

And in the template slot, you can access the arguments via `args` property.

```html
<template>
  <TemplatePromise v-slot="{ args, resolve }">
    <div>{{ args[0] }}</div> <!-- hello -->
    <div>{{ args[1] }}</div> <!-- 123 -->
    <button @click="resolve(true)">OK</button>
  </TemplatePromise>
</template>
```

### Transition

You can use transition to animate the slot.

```html
<script setup lang="ts">
const TemplatePromise = useTemplatePromise<ReturnType>({
  transition: {
    name: 'fade',
    appear: true,
  },
})
</script>

<template>
  <TemplatePromise v-slot="{ resolve }">
    <!-- your UI -->
    <button @click="resolve('ok')">OK</button>
  </TemplatePromise>
</template>

<style scoped>
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to {
  opacity: 0;
}
</style>
```

Learn more about [Vue Transition](https://v3.vuejs.org/guide/transitions-overview.html).

## Thanks

Thanks to [@johnsoncodehk](https://github.com/johnsoncodehk) for making Volar and the help to make it type safe.

## FAQ

### Why?

The common approach to call a dialog or a model programmatically would be like this:

```ts
const dialog = useDialog()
const result = await dialog.open({
  title: 'Hello',
  content: 'World',
})
```

This could work nicely by sending those infomation to top level component and let it render the dialog. However, it limits the flexibility you could express in the UI. For example, if you want the title to be red, if you want extra buttons, etc. You might end up with a lot of options like:

```ts
const result = await dialog.open({
  title: 'Hello',
  titleClass: 'text-red',
  content: 'World',
  contentClass: 'text-blue text-sm',
  buttons: [
    { text: 'OK', class: 'bg-red', onClick: () => {} },
    { text: 'Cancel', class: 'bg-blue', onClick: () => {} },
  ],
  // ...
})
```

Even this is not flexible enough. If you want more, you might end up with manual render function.

```ts
const result = await dialog.open({
  title: 'Hello',
  contentSlot: () => h(MyComponent, { content }),
})
```

This is like reinventing a new DSL in the script to express the UI template.

So this library is introduce to **express the UI in Vue's template instead of the script**, as where they are supposed to be, and being able to be called programmatically.

### VueUse?

Probably. I am having this as a separate package to test how this idea works. If it ends up working well, we will consider moving it into VueUse directly.

## Sponsors

<p align="center">
  <a href="https://cdn.jsdelivr.net/gh/antfu/static/sponsors.svg">
    <img src='https://cdn.jsdelivr.net/gh/antfu/static/sponsors.svg'/>
  </a>
</p>

## License

[MIT](./LICENSE) License © 2022 [Anthony Fu](https://github.com/antfu)
