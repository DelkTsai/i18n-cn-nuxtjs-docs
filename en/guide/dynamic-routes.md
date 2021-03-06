---
title: Dynamic Routes
description: To define a dynamic route with a param in Nuxt.js, you need to define a Vue file prefixed by an underscore.
---

> To define a dynamic route with a param, you need to define a Vue file **prefixed by an underscore**.

## Directory Structure

This file tree:

```bash
pages/
--| users/
-----| _id.vue
-----| index.vue
```

will automatically generate:

```js
router: {
  routes: [
    {
      name: 'users',
      path: '/users',
      component: 'pages/users/index.vue'
    },
    {
      name: 'users-id',
      path: '/users/:id',
      component: 'pages/users/_id.vue'
    }
  ]
}
```

## Validate Route Params

```js
validate({ params, query }) {
  return true // if the params are valid
  return false // will stop Nuxt.js to render the route and display the error page
}
```

Nuxt.js lets you define a validator method inside your dynamic route component (In this example: `pages/users/_id.vue`).

If the validate method does not return `true`, Nuxt.js will automatically load the 404 error page.

```js
<script>
export default {
  validate ({ params }) {
    // Must be a number
    return /^\d+$/.test(params.id)
  }
}
</script>
```
