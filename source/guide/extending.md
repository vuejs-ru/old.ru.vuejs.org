title: Расширение Vue
type: guide
order: 14
---  

## Расширение Примесями

Примеси это гибкий способ для распространения и повторного использования функциональности для компонентов Vue. Можно писать примеси как простой объект опций компонента Vue:

``` js
// mixin.js
module.exports = {
  created: function () {
    this.hello()
  },
  methods: {
    hello: function () {
      console.log('hello from mixin!')
    }
  }
}
```

``` js
// test.js
var myMixin = require('./mixin')
var Component = Vue.extend({
  mixins: [myMixin]
})
var component = new Component() // -> "hello from mixin!"
```

## Расширение Плагинами

Обычно плагины добавляют глобальную функциональность для Vue.

### Создание Плагина

Существует несколько типов плагинов (в зависимости от воздействия):

1. Добавляет один или более глобальных методов, например, [vue-element](https://github.com/vuejs/vue-element)
2. Добавляет одину или более глобальную сущность: директиву/фильтр/переход и т.п., например, [vue-touch](https://github.com/vuejs/vue-touch)
3. Добавляет метод к экземпляру Vue, добавляя их к Vue.prototype. Договорённость здесь, что методы объекта Vue должны иметь префикс `$`, так чтобы не конфликтовать с пользовательскими данными и методами.

``` js
exports.install = function (Vue, options) {
  Vue.myGlobalMethod = ...          // 1
  Vue.directive('my-directive', {}) // 2
  Vue.prototype.$myMethod = ...     // 3
}
```

### Использование Плагина

Пример с использованием модулей CommonJS:
``` js
var vueTouch = require('vue-touch')
// use the plugin globally
Vue.use(vueTouch)
```

Также можно передать в плагин дополнительные параметры:

```js
Vue.use(require('my-plugin'), {
  /* pass in additional options */
})
```

## Существующие Плагины и Инструменты

- [vue-router](https://github.com/vuejs/vue-router): Официальный роутер для Vue.js. Глубоко интегрирован с ядром Vue.js, чтобы облегчить процесс создания одностраничных приложений. 
- [vue-resource](https://github.com/vuejs/vue-resource): Плагин, предоставляющий сервисы для создания веб запросов и обработку ответов, используя XMLHttpRequest или JSONP.
- [vue-async-data](https://github.com/vuejs/vue-async-data): Плагин для асинхронной загрузки начальных данных.
- [vue-validator](https://github.com/vuejs/vue-validator): Плагин для валидации форм.
- [vue-devtools](https://github.com/vuejs/vue-devtools): Расширение для инструментов разработчка в браузере Chrome, для отладки Vue.js приложений.
- [vue-touch](https://github.com/vuejs/vue-touch): Добавляет директиву отслеживания жестов и касаний посредством Hammer.js.
- [vue-element](https://github.com/vuejs/vue-element): Регистрирует Пользовательские Элементы для  Vue.js.
- [Список сторонних инструментов](https://github.com/yyx990803/vue/wiki/User-Contributed-Components-&-Tools)

Далее: [Советы и Лучшие Практики](/guide/best-practices.html).

*[Примесями]: Mixins
*[Примеси]: Mixins
*[объект опций компонента]: component option object
*[одностраничных приложений]: Single Page Applications
*[Пользовательские Элементы]: Custom Elements