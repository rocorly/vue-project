Rationale

Design Solution and Functionality

My web app design is based on a visual image gallery, aimed to showcase film posters pulled from the National Film and Sound Archive of Australia (NFSA). Its functionality utilises a search and filter function, filtering through genres and forms which change the output of results like other search functions on a web browser or site. A reset function appears under the search bar allowing to clear search results. The data is pulled from the searchData.ts store and runs through the fetchData() [(HelloWorld.vue) line 24-31] function running through the $data object representing all properties stored in the instance which access the title and names. The src attribute is attached to the result value which grab title and name properties. The v-if directive renders the image in the file path ‘preview’ at index ([0]) in combination of the NFSA’s media server through the $data object (imgURL) and the string as the value for the src attribute. The v-bind directive allows us to bind a HTML attribute to data in the Vue instance, grabbing the imgURL defined in the data [(HelloWorld.vue) line 16], then displays it on the page. (w3schools, n.d.) Responsiveness has been included, ensuring functionality for different screen sizes via media quires [(HelloWorld.vue) line 304-390].

Filtering genres and forms is achieved through a value attribute specifying an input element, the checkbox. The v-model is binded to “selectedCategories” as a part of the filteredItems method. [(HelloWord.vue)lines 142, 145, 148, 151, 158, 161, 164] When checked the selected element expand to the DOM property and activates the event listeners that filter the items. (Vue, 2014)

Frameworks and Resources

The website has been built with Vue.js, CSS and HTML. In comparison to the original mock-up layouts, few features are missing. This includes the dynamic time-period range slider as well as a dropdown menu which would hide the filter options. However, this feature functionality revolves around a sticky navigation which is not present. During pre-production, other frameworks were considered including React, Angular and jQuery, however I have chosen Vue.js due to the resources provided by the tutorials as well as its streamlined functionality. Specifically, modules 5.4, 6.2 and 6.3. Tutorials and guides from the Vue.js site was used in conjunction to tutorials which go over the same content, essentials and components.

Reflection

Whilst the cite is completely functional, I would have liked to add some additional interaction, specifically around the image gallery. Addition of a lightbox function would enhance the user experience by adding additional functionality and interactability. Additionally, the clear results function could reset the filters, which would streamline the functionality of the app. Further implementing components, into the site may also allow for additional functions without requiring a separate web page, inclusion of a search function with greater capabilities. With more options such as place of origin, language and dropdown functions giving access to all filter tags shown as options on the sidebar of the NFSA site. The API elements and JavaScript have been written with the support of the material given in the tutorials. Whilst some CSS layout and formatting have been supported by W3schools and YouTube.

Vue (2022). Introduction | Vue.js. [online] vuejs.org. Available at: https://vuejs.org/guide/introduction.html.
VueJS (n.d.). Component v-model | Vue.js. [online] vuejs.org. Available at: https://vuejs.org/guide/components/v-model.html.
VueJS (2014). Vue.js. [online] Vuejs.org. Available at: https://vuejs.org/guide/essentials/forms.
w3schools (n.d.). Vue Tutorial. [online] www.w3schools.com. Available at: https://www.w3schools.com/vue/index.php.

<!-- # vue-project

This template should help get you started developing with Vue 3 in Vite.

## Recommended IDE Setup

[VSCode](https://code.visualstudio.com/) + [Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (and disable Vetur).

## Type Support for `.vue` Imports in TS

TypeScript cannot handle type information for `.vue` imports by default, so we replace the `tsc` CLI with `vue-tsc` for type checking. In editors, we need [Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar) to make the TypeScript language service aware of `.vue` types.

## Customize configuration

See [Vite Configuration Reference](https://vitejs.dev/config/).

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Type-Check, Compile and Minify for Production

```sh
npm run build
```

### Run Unit Tests with [Vitest](https://vitest.dev/)

```sh
npm run test:unit
```

### Lint with [ESLint](https://eslint.org/)

```sh
npm run lint
``` -->
