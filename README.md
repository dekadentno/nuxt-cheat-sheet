# nuxt-cheat-sheet
:green_book: My cheat sheet for nuxt most basic stuff

Nuxt.js is a higher-level framework that builds on top of Vue. It simplifies the development of universal or single page Vue apps.
Nuxt.js is not a server side framework. It runs on the servers. It renders the first page and after the first page is renderd, the Vue.js app takes over. 

Useful links:
* [Creating a nuxt app + documentation](https://nuxtjs.org/guide/installation)
* 

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
  * Static routes will be named ```index.html``` in its corresponding folder (folder structure)
  * Dynamic routes need to have prefixes (e.g.: ```_id.vue```)
  * Access route object via ```$route```
  * use ```nuxt-link``` to navigate between pages, so an extra request to the server call won't be sent
* components - reusable components. You can't use either asyncData or fetch in these components.
* layouts - includes your application layouts
  
### Routing
There is no ```router.js``` file. Routes are automatically generated from the files placed in the ```pages``` directory. 
* Static routes will be separated in their directories and named ```index.html```. 
* Dynamic routes are placed in their folders and must have an underscore prefix. 
* Nested routes will be named just as their parent directory (e.g.: Blog.vue). Don't forget to include <nuxt-child/> inside the parent component (.vue file).

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
