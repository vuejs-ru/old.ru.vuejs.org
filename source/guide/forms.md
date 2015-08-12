title: Управление Формами
type: guide
order: 7
---

## Основы

Вы можете использовать директиву `v-model` для установления двусторонней привязки данных к полям формы. В зависимости от типа элемента, Vue автоматически выберет способ его обновления.

**Пример**

``` html
<form id="demo">
  <!-- text -->
  <p>
    <input type="text" v-model="msg">
    {{msg}}
  </p>
  <!-- checkbox -->
  <p>
    <input type="checkbox" v-model="checked">
    {{checked ? "yes" : "no"}}
  </p>
  <!-- radio buttons -->
  <p>
    <input type="radio" name="picked" value="one" v-model="picked">
    <input type="radio" name="picked" value="two" v-model="picked">
    {{picked}}
  </p>
  <!-- select -->
  <p>
    <select v-model="selected">
      <option>one</option>
      <option>two</option>
    </select>
    {{selected}}
  </p>
  <!-- multiple select -->
  <p>
    <select v-model="multiSelect" multiple>
      <option>one</option>
      <option>two</option>
      <option>three</option>
    </select>
    {{multiSelect}}
  </p>
  <p><pre>data: {{$data | json 2}}</pre></p>
</form>
```

``` js
new Vue({
  el: '#demo',
  data: {
    msg      : 'hi!',
    checked  : true,
    picked   : 'one',
    selected : 'two',
    multiSelect: ['one', 'three']
  }
})
```

**Результат**

<form id="demo"><p><input type="text" v-model="msg"> {&#123;msg&#125;}</p><p><input type="checkbox" v-model="checked"> {&#123;checked ? &quot;yes&quot; : &quot;no&quot;&#125;}</p><p><input type="radio" v-model="picked" name="picked" value="one"><input type="radio" v-model="picked" name="picked" value="two"> {&#123;picked&#125;}</p><p><select v-model="selected"><option>one</option><option>two</option></select> {&#123;selected&#125;}</p><p><select v-model="multiSelect" multiple><option>one</option><option>two</option><option>three</option></select>{&#123;multiSelect&#125;}</p><p>data:<pre style="font-size:13px;background:transparent;line-height:1.5em">{&#123;$data | json 2&#125;}</pre></p></form>
<script>
new Vue({
  el: '#demo',
  data: {
    msg      : 'hi!',
    checked  : true,
    picked   : 'one',
    selected : 'two',
    multiSelect: ['one', 'three']
  }
})
</script>

## Отложенное Обновление

По умолчанию `v-model` синхронизирует поле ввода со свойством после каждого события `input`. Вы можете добавить атрибут `lazy` чтобы синхронизация происходила после события `change`:

``` html
<!-- synced after "change" instead of "input" -->
<input v-model="msg" lazy>
```

## Передача Значение в Качестве Числа

Если вам требуется чтобы значение поля ввода сохранялось как число, вы можете использовать атрибут `number`:

``` html
<input v-model="age" number>
```

## Динамические Значения для Select

Когда вам требуется динамически выводить список опций для элемента `<select>`, рекомендуется использовать атрибут `options` совместно с `v-model`, таким образом при динамическом изменение опции, `v-model` корректно синхронизируется:

``` html
<select v-model="selected" options="myOptions"></select>
```

`myOptions` должен содержать Массив объектов-опций.

Массив может содержать либо строки, либо объекты в следующем формате `{text:'', value:''}`. Это позволяет разделить значение и выводимый текст:

``` js
[
  { text: 'A', value: 'a' },
  { text: 'B', value: 'b' }
]
```

Будет преобразовано в:

``` html
<select>
  <option value="a">A</option>
  <option value="b">B</option>
</select>
```

Также объект может быть представлен как `{ label:'', options:[...] }`. В таком случае он будет выведен как `<optgroup>`:

``` js
[
  { label: 'A', options: ['a', 'b']},
  { label: 'B', options: ['c', 'd']}
]
```

Will render:

``` html
<select>
  <optgroup label="A">
    <option value="a">a</option>
    <option value="b">b</option>
  </optgroup>
  <optgroup label="B">
    <option value="c">c</option>
    <option value="d">d</option>
  </optgroup>
</select>
```

### Фильтр Опций

Вероятно, что ваши исходные данные не приходят в необходимом формат и вам необходимо преобразовать данные для генерации динамических опций. Для того, чтобы придерживаться DRY-подхода, параметр `options` поддерживает фильтры, а также возможно вынести логику трансформации в переиспользуемые [пользовательские фильтры](/guide/custom-filter.html):

``` js
Vue.filter('extract', function (value, keyToExtract) {
  return value.map(function (item) {
    return item[keyToExtract]
  })
})
```

``` html
<select
  v-model="selectedUser"
  options="users | extract 'name'">
</select>
```

Данные фильтр из примера преобразует данные `[{ name: 'Bruce' }, { name: 'Chuck' }]` в `['Bruce', 'Chuck']`.

### Статические опции по умолчанию

> начиная с 0.12.10+

Можно задать одну статическую опцию по умолчанию, дополнительно к динамически генерируемым:

``` html
<select v-model="selectedUser" options="users">
  <option value="">Выберите пользователя...</option>
</select>
```

Динамические опции, созданные из `users`, будут добавлены после Статической опции. Статическая опция будет выбрана по умолчанию, если значение `v-model` ложно (исключая `0`).

## Задержка Ввода

Атрибут `debounce` позволяет установить минимальную задержку, после каждого нажатия клавиши, до запуска процедуры обновления значения. Это может быть удобно, когда требуется произвести сложные вычисления при каждом обновлении, к примеру сделать Ajax запрос для организации автоматического дополнения:

``` html
<input v-model="msg" debounce="500">
```

**Результат**

<div id="debounce-demo" class="demo">{&#123;msg&#125;}<br><input v-model="msg" debounce="500"></div>
<script>
new Vue({
  el:'#debounce-demo',
  data: { msg: 'edit me' }
})
</script>

Обратите внимание, что параметр `debounce` не задерживает события пользовательского ввода: он задерживает операции записи данных. Поэтому используйте `vm.$watch()` для реакции на фактическое  изменнение данных при использовании `debounce`.

Далее: [Вычисляемые Свойства](/guide/computed.html).
