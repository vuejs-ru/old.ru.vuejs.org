title: Создаём Большие Приложения
type: guide
order: 13
---

Vue.js создан с учетом максимальной гибкости - это всего лишь библиотека для создания интрефейса и не навязывает никаких архитектурных решений. В то время как это полезно для быстрого прототипирования, это вызвать затруднения для разработчиков с небольшим опытом при построении широкомасштабных приложений. Далее приведены идеи по поводу ориганизации широкомасштабных приложений с Vue.js.

## Использование Модулей

В то время как самостоятельная сборка Vue.js может быть использована в глобальной области видимости, чаще лучше использовать модульный подход и системы сборки модулей для организации кода. Рекоммедуется писать исходный код в виде модулей CommonJS (формат испольуемый в Node.js, также испольуется для исходного кода Vue.js) и собирать их в пакеты с помощью инструментов [Webpack](http://webpack.github.io/) или [Browserify](http://browserify.org/).

Webpack и Browserify это больше, чем просто сборщики модулей. Оба инструмента предоставляют API для трансформации исходного кода с помощью различных препроцессоров. Например, можно писать свой код используя новшества синтаксиса ES6/7 с [babel-loader](https://github.com/babel/babel-loader) или [babelify](https://github.com/babel/babelify).

## Монолитные Компоненты

Типичный проект с Vue.js подразумевает разделение кода на множество маленькиех компонент. И было бы хорошо инкапсулировать для каждого компонента стили CSS, шаблоны JavaScript код в одном месте. Как упомянуто  выше, при использовании Webpack или Browserify, с необходимыми трансформациями кода можно писать компоненты следующим образом:

<img src="images/vue-component.png">

При использовании препроцессоров можно делать даже так:

<img src="images/vue-component-with-pre-processors.png">

Можно построить такие монолитные компоненты при помощи Webpack + [vue-loader](https://github.com/vuejs/vue-loader) или Browserify + [vueify](https://github.com/vuejs/vueify). Рекоммендуется использовать Webpack т.к. API загрузки Webpack's позволяет лучшее отслеживание зависимостей файлов и кеширование при использовании препроцессоров.

Примеры настройки билдов на GitHub:

- [Webpack + vue-loader](https://github.com/vuejs/vue-loader-example)
- [Browserify + vueify](https://github.com/vuejs/vueify-example)

## Маршрутизация

Для Одностраничных Приложений рекоммендуется использовать [официальный vue-router](https://github.com/vuejs/vue-router), который пока в стадии технической разработки. Детали в [документации](http://vuejs.github.io/vue-router/).

Если вам необходима очень простая маршрутизация, можно реализовать её установив слушатели на событие смены хэш и используя динамический компонент:

**Пример:**

``` html
<div id="app">
  <component is="{{currentView}}"></component>
</div>
```

``` js
Vue.component('home', { /* ... */ })
Vue.component('page1', { /* ... */ })
var app = new Vue({
  el: '#app',
  data: {
    currentView: 'home'
  }
})
// Switching pages in your route handler:
app.currentView = 'page1'
```

Используя данный механизм также легко подключить сторонние бибилотеки для маршрутизации, такие как [Page.js](https://github.com/visionmedia/page.js) или [Director](https://github.com/flatiron/director).

## Связь с Сервером

Во всех объектах Vue можно сериализовать их сырые данные `$data` с помощью `JSON.stringify()` без дополнительных усилий. Сообщество предоставило плагин [vue-resource](https://github.com/vuejs/vue-resource), упрощающий работу с RESTful APIs. Также можно использовать любую Ajax библиотеку, например, `$.ajax` или [SuperAgent](https://github.com/visionmedia/superagent). Vue.js также хорошо работает с такими сервисами без бекэнда как Firebase и Parse.

## Unit Тестирование

Работают любые инструменты, совместимые с билд системами на основе модулей CommonJS. Рекоммендуется использовать систему запуска тестов [Karma](http://karma-runner.github.io/0.12/index.html) вместе с её [препроцессором CommonJS](https://github.com/karma-runner/karma-commonjs) для модульного тестирования кода.

Наилучший подход - экспортировать сырые опции / функции внутрь модуля. Пример:

``` js
// my-component.js
module.exports = {
  template: '<span>{{msg}}</span>',
  data: function () {
    return {
      msg: 'hello!'
    }
  }
  created: function () {
    console.log('my-component created!')
  }
}
```

Вы сможете использовать такой файл следующим образом:

``` js
// main.js
var Vue = require('vue')
var app = new Vue({
  el: '#app',
  data: { /* ... */ },
  components: {
    'my-component': require('./my-component')
  }
})
```

А протестировать этот модуль так:

``` js
// Some Jasmine 2.0 tests
describe('my-component', function () {  
  // require source module
  var myComponent = require('../src/my-component')
  it('should have a created hook', function () {
    expect(typeof myComponent.created).toBe('function')
  })
  it('should set correct default data', function () {
    expect(typeof myComponent.data).toBe('function')
    var defaultData = myComponent.data()
    expect(defaultData.message).toBe('hello!')
  })
})
```

<p class="tip">Так как директивы Vue.js реагируют на изменения данных асинхронно, во время проверки состояния DOM элементов после изменения данных, вам необходимо делать это в коллбеке `Vue.nextTick`.</p>

## Подготовка к Продакш Окружению

The minified standalone build of Vue.js has already stripped out all the warnings for you for a smaller file size, but when you are using tools like Browserify or Webpack to build Vue.js applications, it's not so obvious how to do that.

Starting in 0.12.8, it is quite simple to configure the tools to strip out the warnings:

### Webpack

Use Webpack's [DefinePlugin](http://webpack.github.io/docs/list-of-plugins.html#defineplugin) to indicate a production environment, so that warning blocks can be automatically dropped by UglifyJS during minification. Example config:

``` js
var webpack = require('webpack')

module.exports = {
  // ...
  plugins: [
    // ...
    new webpack.DefinePlugin({
      'process.env': {
        NODE_ENV: '"production"'
      }
    }),
    new webpack.optimize.UglifyJsPlugin({
      compress: {
        warnings: false
      }
    })
  ]
}
```

### Browserify

Just run your bundling command with `NODE_ENV` set to `"production"`. Vue automatically applies [envify](https://github.com/hughsk/envify) transform to itself and makes warning blocks unreachable. For example:

``` bash
NODE_ENV=production browserify -e main.js | uglifyjs -c -m > build.js
```

## An Example

The [Vue.js Hackernews Clone](https://github.com/yyx990803/vue-hackernews) is an example application that uses Webpack + vue-loader for code organization, Director.js for routing, and HackerNews' official Firebase API as the backend. It's by no means a big application, but it demonstrates the combined usage of the concepts discussed on this page.

Next: [Extending Vue](/guide/extending.html).

*[Использование Модулей]: Modularization
*[Монолитные Компоненты]: Single File Components
*[Маршрутизация]: Routing
*[Одностраничных Приложений]: Single Page Applications