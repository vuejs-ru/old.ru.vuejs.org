title: Установка
type: guide
order: 1
vue_version: 0.12.9
dev_size: "227.12"
min_size: "68.64"
gz_size: "22.43"
---

> **Примечание:** Vue.js не поддерживает IE8 и ниже.

## Самостоятельная установка

Просто загрузите и добавьте на станицу с помощью тега script `Vue` будет зарегистрирована как глобальная переменная.

<div id="downloads">
<a class="button" href="https://raw.github.com/yyx990803/vue/{{vue_version}}/dist/vue.js" download>Версия для разработки</a><span class="light info">{{dev_size}}kb, много комментариев и сообщений отладки/предупреждений.</span>

<a class="button" href="https://raw.github.com/yyx990803/vue/{{vue_version}}/dist/vue.min.js" download>Production версия</a><span class="light info">{{min_size}}kb минифицированная / {{gz_size}}kb сжатая</span>
</div>

### CDN

Также доступна на [jsdelivr](//cdn.jsdelivr.net/vue/{{vue_version}}/vue.min.js) и [cdnjs](//cdnjs.cloudflare.com/ajax/libs/vue/{{vue_version}}/vue.min.js) (требуется некоторое время для обновления, последняя версия может быть недоступна).

### CSP-совместимый билд

Некоторые окружения, такие как Google Chrome Apps, навязывают Content Security Policy (CSP) и не позволяют использовать `new Function()` для обработки выражений. В этом случае вы можете использовать [CSP-совместимый билд](https://github.com/yyx990803/vue/tree/csp/dist).

## NPM

``` bash
$ npm install vue
# for csp-compliant version:
$ npm install vue@csp
# for dev build (from GitHub):
$ npm install yyx990803/vue#dev
```

## Bower

``` bash
# доступны только стабильные версии
$ bower install vue
```

## Загрузчики модулей AMD 

Версия standalone или установленная с помощью Bower обёрнуты с помощью UMD, поэтому могут быть использованы в качестве AMD модуля.

## Готовы?

[Начнём!](/guide/).

