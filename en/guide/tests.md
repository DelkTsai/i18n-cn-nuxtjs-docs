---
title: Tests
description: Testing your application is part of the web development. Nuxt.js helps you to make it as easy as possible.
---

> Testing your application is part of the web development. Nuxt.js helps you to make it as easy as possible.

## End-to-End Testing

[Ava](https://github.com/avajs/ava) is a powerful JavaScript testing framework, mixed with [jsdom](https://github.com/tmpvar/jsdom), we can use them to do end-to-end testing easily.

First, we need to add ava and jsdom as development dependencies:
```bash
npm install --save-dev ava jsdom
```

And add a test script to our `package.json`:

```javascript
"scripts": {
  "test": "ava",
}
```

We are going to write our tests in the `test` folder:
```bash
mkdir test
```

Let's says we have a page in `pages/index.vue`:
```html
<template>
  <h1 class="red">Hello {{ name }}!</h1>
</template>

<script>
export default {
  data () {
    return { name: 'world' }
  }
}
</script>

<style>
.red {
  color: red;
}
</style>
```

When we launch our app with `npm run dev` and open [http://localhost:3000](http://localhost:3000), we can see our red `Hello world!` title.

We add our test file `test/index.test.js`:

```js
import test from 'ava'
import Nuxt from 'nuxt'
import { resolve } from 'path'

// We keep the nuxt and server instance
// So we can close them at the end of the test
let nuxt = null
let server = null

// Init Nuxt.js and create a server listening on localhost:4000
test.before('Init Nuxt.js', async t => {
  const rootDir = resolve(__dirname, '..')
  let config = {}
  try { config = require(resolve(rootDir, 'nuxt.config.js')) } catch (e) {}
  config.rootDir = rootDir // project folder
  config.dev = false // production build
  nuxt = new Nuxt(config)
  await nuxt.build()
  server = new nuxt.Server(nuxt)
  server.listen(4000, 'localhost')
})

// Example of testing only generated html
test('Route / exits and render HTML', async t => {
  let context = {}
  const { html } = await nuxt.renderRoute('/', context)
  t.true(html.includes('<h1 class="red">Hello world!</h1>'))
})

// Example of testing via dom checking
test('Route / exits and render HTML with CSS applied', async t => {
  const window = await nuxt.renderAndGetWindow('http://localhost:4000/')
  const element = window.document.querySelector('.red')
  t.not(element, null)
  t.is(element.textContent, 'Hello world!')
  t.is(element.className, 'red')
  t.is(window.getComputedStyle(element).color, 'red')
})

// Close server and ask nuxt to stop listening to file changes
test.after('Closing server and nuxt.js', t => {
  server.close()
  nuxt.close()
})
```

We can now launch our tests:
```bash
npm test
```

Actually, jsdom has some limitations because of it does not use any browser behind but it will cover most of our tests. If you want to use a browser to test your application, you might want to check [Nightwatch.js](http://nightwatchjs.org).
