# todolist

## Project setup
```
yarn install
```

### Compiles and hot-reloads for development
```
yarn serve
```

### Compiles and minifies for production
```
yarn build
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).

Setup na máquina para iniciar Projeto:

- [ ]  Projeto criado com VueJS-2
- [ ]  Para iniciar projeto são estes os comandos:
- [ ]  npm i
- [ ]  npm run serve
- [ ]  Plugins instalados foram utilizados estes comandos por causa do VueJS 2:
- [ ]  VueJS 2 - Babel - Router
- [ ]  npm install vue bootstrap-vue bootstrap
- [ ]  dentro do main.js adiciona isto
- [ ]  
  <Depois disso, é necessário realizar a importação do Bootstrap-vue no main.js, dessa forma:

```js
import Vue from 'vue'
import { BootstrapVue, IconsPlugin } from 'bootstrap-vue'
Vue.use(BootstrapVue)
Vue.use(IconsPlugin)
``` 

Além dessa importação, para que tudo funcione bem, é necessário importar o css do BootstrapVue:
```js
import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'
``` 
>

para fazer a API fake usa este comando 
banco criado db.json
```JSON
npx json-server --watch db.json
``` 

pode adicionar ele no package.json na parte script nesta maneira
 "backend": "npx json-server --watch db.json"

 depois pode rodar npm r un backend

 Referências

https://undraw.co/illustrations

https://undraw.co/search

https://router.vuejs.org/

https://bootstrap-vue.org/

https://bootstrap-vue.org/docs