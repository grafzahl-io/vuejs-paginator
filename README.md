# Vue.js Paginator [![CircleCI](https://circleci.com/gh/hootlex/vuejs-paginator.svg?style=shield&circle-token=:circle-ci-badge-token)](https://circleci.com/gh/hootlex/vuejs-paginator) [![npm downloads](https://img.shields.io/npm/dt/vuejs-paginator.svg)](https://www.npmjs.com/package/vuejs-paginator) <a href="https://www.npmjs.com/package/vuejs-paginator"><img src="https://img.shields.io/npm/v/vuejs-paginator.svg" alt="Version"></a> [![Software License](https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat)](LICENSE)
> A Vue.js plugin to easily integrate pagination in your projects.

VueJs Paginator is a simple but powerful plugin since it gives you access on how to render the data, instead of using a predefined table.

![vue paginator preview](http://i.imgur.com/2jah1qt.gif)

The way you use it is similar to Laravel's paginator.

## About this fork
This is a fork to make it work in a Spark/laravel environment.
Specifically I changed the $http call to axios.

## Installation
> For Vue 1.* use [v1.0.15](https://github.com/hootlex/vuejs-paginator/tree/v1.0.15).

### Through npm
``` bash
npm install vuejs-paginator --save
```

### From a cdn
```HTML
<script src="https://cdnjs.cloudflare.com/ajax/libs/vuejs-paginator/2.0.0/vuejs-paginator.js"></script>
<!-- or -->
<script src="https://cdn.jsdelivr.net/vuejs-paginator/2.0.0/vuejs-paginator.min.js"></script>
```

## Usage
Use VPaginator in the HTML.
```html
<v-paginator resource_url="api/animals" @update="updateResource"></v-paginator>
```

Prepare the Vue instance.
```js
// if you are not using the cdn version you have to import VuePaginator.
// import VuePaginator from 'vuejs-paginator'
new Vue({
    data () {
      return {
        animals: []
      }
    },
    components: {
        VPaginator: VuePaginator
    },
    methods: {
      updateResource(data){
        this.animals = data
      }
    }
  ...
});
```

### Thats it

Every time a page is changed or fetched, resource variable will contain the returned data.

```html
<ul>
  <li v-for="animal in animals">
    {{ animal.name }}
  </li>
</ul>
```

### Extended functions

#### url_builder Property

There is a new property that allows to pass a function callback that expects an property to pass the page as a number and returns the URL string to the page.

This property is required if you want to use the "first" or "last" button. Or if you want to use the "Jump To" function.

```html
<v-paginator
    :options="paginatorOptions"
    :url_builder="getSyncProductLoadUrl"
    :resource_url="getSyncProductLoadUrl(1)"
    ref="syncProductPagination"
    @update="loadSyncProducts">
</v-paginator>
```

This is a function, the passed callback could look like.

```
/**
 * returns the sync product url for the paginator
 *
 * @param int page
 */
getSyncProductLoadUrl: function(page) {
    return '/products/index/page/' + page;
},
```

### Jump-To Button

To display a separate element with an input field where the user can input a specific
number to jump to a page, you can pass the following property to the options prop.

```
paginatorOptions: {
  show_jump_to_input: true,
}
```

### Enable "first" and "last" Buttons

To display new buttons for jumping directly to the first or the last page you need to pass the following option:

```
paginatorOptions: {
  show_first_last_buttons: true,
}
```

### New options to overwrite / i18n the texts

To alternate the texts which are used in the plugin, you can pass the following options:

```
paginatorOptions: {
  jump_to_button_text: 'Jump to',
  previous_button_text: 'Previous',
  next_button_text: 'Next',
  page_label_text: 'Page',
  of_label_text: 'of'
}
```

### Documentation
[Here you can find the detailed Documentation](http://hootlex.github.io/vuejs-paginator/)

## Build Setup

``` bash
# install dependencies
npm install

# build for production with minification
npm run build

# run unit tests
npm run unit
# run all tests
npm test
```
