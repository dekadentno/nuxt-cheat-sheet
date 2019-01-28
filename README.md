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
  ** Static routes will be named ```index.html``` in its corresponding folder (folder structure)
  ** Dynamic routes need to have prefixes (e.g.: ```_id.vue```)
  ** Access route object via ```$route```
