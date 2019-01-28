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
Using the asyncData object in pages:
```javascript
export default {
  async asyncData ({ params }) {
    let { data } = await axios.get(`https://my-api/posts/${params.id}`)
    return { title: data.title } // title is a variable in our component data
  }
}
```
