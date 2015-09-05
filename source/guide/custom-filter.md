title: Пользовательские фильтры
type: guide
order: 10
---

## Основы

Как и с пользовательскими директивами, можно зарегистрировать пользовательские фильтры при помощи глобального метода `Vue.filter()`, передав **ID фильтра** и **функцию-фильтр**. Функция-фильтр принимает значение как аргумент и возвращает трансформированное значение:

``` js
Vue.filter('reverse', function (value) {
  return value.split('').reverse().join('')
})
```

``` html
<!-- 'abc' => 'cba' -->
<span v-text="message | reverse"></span>
```

Функция-фильтр принимает также любые строковые аргументы:

``` js
Vue.filter('wrap', function (value, begin, end) {
  return begin + value + end
})
```

``` html
<!-- 'hello' => 'before hello after' -->
<span v-text="message | wrap 'before' 'after'"></span>
```

## Двусторонние Фильтры

До сих пор мы использовали фильтры для трансформации значений из модели перед отображением их в предсталении. Но также возможно определить фильтр для трансформации данных перед записью их обратно из представления (элементов ввода) в модель:

``` js
Vue.filter('currencyDisplay', {
  currencyDisplay: {
    // model -> view
    // для вывода в шаблон
    read: function(val) {
      return '$'+val.toFixed(2)
    },
    // view -> model
    // для записи обновлённых данных в модель
    write: function(val, oldVal) {
      var number = +val.replace(/[^\d.]/g, '')
      return isNaN(number) ? 0 : number
    }
  }
}
```

Демо:

{% raw %}
<div id="two-way-filter-demo" class="demo">
  <input type="text" v-model="money | currencyDisplay">
  <p>Model value: {{money}}</p>
</div>
<script>
new Vue({
  el: '#two-way-filter-demo',
  data: {
    money: 123.45
  },
  filters: {
    currencyDisplay: {
      read: function(val) {
        return '$'+val.toFixed(2)
      },
      write: function(val, oldVal) {
        var number = +val.replace(/[^\d.]/g, '')
        return isNaN(number) ? 0 : number
      }
    }
  }
})
</script>
{% endraw %}

## Динамические Аргументы

Если аргументы фильтра не взяты в кавычки, они будут автоматически обработаны в контексте текущей МОдели Представления. Также функция-фильтр всегда вызывается в контексте `this` текущей Модели Представления. Например:

``` html
<input v-model="userInput">
<span>{{msg | concat userInput}}</span>
```

``` js
Vue.filter('concat', function (value, input) {
  // here `input` === `this.userInput`
  return value + input
})
```

Для этого простого примера мы можем достичь результата просто с выражением, но для более сложных процедур, требущих не одно выражение, необходимо положить их в вычисляемое свойство или пользовательский фильтр.

Встроенные фильтры `filterBy` и `orderBy` производят нетривиальные операции над переданным Массивом и ссылаются на текущее состояние родительского экземпляра Vue.

Итак! Пришло время узнать как работает [Система Компонентов](/guide/components.html).
