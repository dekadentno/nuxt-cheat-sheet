# nuxt-cheat-sheet
:green_book: My cheat sheet for nuxt most basic stuff

Nuxt.js is a higher-level framework that builds on top of Vue. It simplifies the development of universal or single page Vue apps.
Nuxt.js is not a server side framework. It runs on the servers. It renders the first page and after the first page is renderd, the Vue.js app takes over. 

## Common terms
* pre rendering - app is rendered or generated upfront. Pre rendering is not the same as SSR because you don't need a server because the app is rendered in static html files.
  * client side rendering - javascript - side effects like blinking, load time etc. 

Useful links:
* [Creating a nuxt app + documentation](https://nuxtjs.org/guide/installation)
* [Awesome nuxt](https://github.com/nuxt-community/awesome-nuxt)
* [Axios for nuxt](https://axios.nuxtjs.org/) - interceptors, headers...
* [Using env variables in nuxt](https://samuelcoe.com/blog/nuxt-dotenv/)

## How nuxt works
```
                  +------------------+
                  |                  |
       +----------+     VUE APP      |
       |          |                  |
       |          +---------+--------+
       |                    ^
       |                    |
       |                    |
       v                    +
                       INDEX.HTML
+--------------+ <-------------------  +--------------+
|              |                       |              |
|    CLIENT    |                       |    SERVER    |
|              |         REQ           |              |
+--------------+ --------------------> +--------------+
```

# Directory structure
* pages - contains your Application Views and Routes. The framework reads all the .vue files inside this directory and creates the application router.
  * Static routes (the name is known) will be named ```index.html``` in its corresponding folder (folder structure)
  * Dynamic routes (path is dynamic) need to have prefixes (e.g.: ```_id.vue```)
  * Access route object via ```$route```
  * use ```nuxt-link``` to navigate between pages, so an extra request to the server call won't be sent
* components - reusable components. You can't use either asyncData or fetch in these components.
* layouts - includes your application layouts
  
### Routing
There is no ```router.js``` file. Routes are automatically generated from the files placed in the ```pages``` directory. 
* Static routes will be separated in their directories and named ```index.html```. 
* Dynamic routes are placed in their folders and must have an underscore prefix. 
* Nested routes will be named just as their parent directory (e.g.: Blog.vue). Don't forget to include <nuxt-child/> inside the parent component (.vue file).

Prerendering dynamic routes - explaination cah be found [here](https://nuxtjs.org/api/configuration-generate#the-generate-property)

### Validate routes
Nuxt.js lets you define a validator method inside your dynamic route component. Nuxt.js will automatically load the 404 error page or 500 error page in case of an error. More details [here](https://nuxtjs.org/api/pages-validate)
```javascript
export default {
  validate ({ params }) {
    // Must be a number
    return /^\d+$/.test(params.id)
  }
}
```

### Async Data
Data will be ready before the page is served to the client.
Using the asyncData object in pages:
```javascript
export default {
  async asyncData ({ params }) {
    let { data } = await axios.get(`https://my-api/posts/${params.id}`)
    return { title: data.title } // title is a variable in our component data
  }
}
```
__You do NOT have access of the component instance through this inside asyncData because it is called before initiating the component.__

### The _fetch_ method
The fetch method is used to fill the store before rendering the page, it's like the ```asyncData``` method except it doesn't set the component data. API calls take place on the server side.
```javascript
// posts.js in the store
export const state = () => ({
 all: []
})

export const actions = {
 async fetchAllPosts({commit}) {
  let posts = await this.$axios.$get('posts')
  commit('setPosts', posts)
 }
}

export const mutations = {
 setPosts (state, posts) {
  state.all = posts
 }
}
```
```javascript
// index.vue in the posts directory of the pages
async fetch ({store}) {
 await store.dispatch('posts/fetchAllPosts')
},
computed: {
 posts() {
  return this.$store.state.posts.all
 }
}
```

## 301 redirects
1. Create a list of 301 redirections in a json file at project root:

```js
// 301.json 

[
  { "from": "/old", "to": "/new" },
  { "from": "/veryold.html", "to": "/verynew" },
  { "from": "/realy/too-old.html", "to": "/new" }
]
```

2. Create a server middleware for 301 redirect
```js
// middleware/seo.js

const redirects = require('../301.json')

module.exports = function (req, res, next) {
  const redirect = redirects.find(r => r.from === req.url)
  if (redirect) {
    console.log(`redirect: ${redirect.from} => ${redirect.to}`)
    res.writeHead(301, { Location: redirect.to })
    res.end()
  } else {
    next()
  }
}
```

3. Add the server middleware in your config file:
```js
// nuxt.config.js

module.exports = {
  serverMiddleware: [
    '~/middleware/seo.js'
  ],
 ...
}
```

## Custom 404 page 

1. Create an error.vue file and place it into the layouts folder.

```js
// layouts/error.vue

<template>
  <div class="nuxt-error">
    <component :is="errorPage" :error="error" />
  </div>
</template>
<script>
import error404 from '~/pages/404.vue'
export default {
  name: 'Error',
  layout: 'default',
  props: {
    error: {
      type: Object,
      default: () => {}
    }
  },
  computed: {
    errorPage() {
      if (this.error.statusCode === 404) {
        return error404
      }
      // catch everything else
      return error500
    },
  }
}
</script>
```

2. Create a 404.vue file in the pages folder. Style this file as desired. Placing a 404.vue file into the pages folder will override the 404.html file generated by default, which is a blank screen with a loader.
```js
// pages/404.vue

<template>
  <div>
    <h1>This page doesn’t exist</h1>
    <p>The link you clicked on may be broken or no longer exist.</p>
  </div>
</template>

<script>
  export default {
    name: 'Error404',
    props: {
      error: {
        type: Object,
        default: () => {},
      },
    },
  };
</script>
```

3. Make sure that nuxt generates a 404.html file instead of /404/index.html file automatically by editing your nuxt.config.js file.
```js
// nuxt.config.js

module.exports = {
  generate: {
    /*routes: ['404']*/
    //fallback: "404.html"
    //subFolders: false,
    fallback: true
  },
 ...
}
```
