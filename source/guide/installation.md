title: Установка
type: guide
order: 1
vue_version: 0.12.1
dev_size: 214.12
min_size: 65.67
gz_size: 21.43
---

> **Примечание:** Vue.js не поддерживает IE8 и ниже.

## Standalone

Просто загрузите и добавьте на станицу с помощю тега script `Vue` будет зарегистрирована как глобальная переменная.

<div id="downloads">
<a class="button" href="https://raw.github.com/yyx990803/vue/{{vue_version}}/dist/vue.js" download>Версия для разработки</a><span class="light info">{{dev_size}}kb, много комментариев и сообщений отладки/предупреждений.</span>

<a class="button" href="https://raw.github.com/yyx990803/vue/{{vue_version}}/dist/vue.min.js" download>Production версия</a><span class="light info">{{min_size}}kb минифицированная / {{gz_size}}kb сжатая</span>
</div>

Также доступна на [cdnjs](//cdnjs.cloudflare.com/ajax/libs/vue/{{vue_version}}/vue.min.js) (требуется некоторое время для обновления, последняя версия может быть недоступна).

## NPM

``` bash
$ npm install vue
# for edge version:
$ npm install yyx990803/vue#dev
```

## Bower

``` bash
# доступны только стабильные версии
$ bower install vue
```

## Duo

```js
var Vue = require('yyx990803/vue')
// Для версии edge:
var Vue = require('yyx990803/vue@dev')
```

## Как модуль AMD 

Версия standalone или установленная с помощью Bower обёрнуты с помощью UMD поэтому могут быть использованы в качестве AMD модуля.

## Готовы?

[Давайте Начнём](/guide/).
