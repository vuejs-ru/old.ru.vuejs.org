title: Анимированные переходы
type: guide
order: 12
---

Vue позволяет анимировать вставку и изъятие элементов из дерева DOM с помощью CSS-переходов. Vue автоматически применит соответствующие классы к элементу и отменит их по окончании анимации. Кроме этого, переход можно описать с помощью JavaScript.

При применении директивы `v-transition="my-transition"` произойдет следующее:

1. Vue попытается найти одноименный объект, описывающий переход. Такой объект представляет собой набор функций-хуков, и должен быть либо зарегистрирован глобально через `Vue.transition(id, hooks)`, либо указан под именем `"my-transition"` в разделе `transitions` в опциях экземпляра. Если объект найден, Vue будет вызывать функции-хуки из этого объекта в соответствующие моменты перехода.

2. Vue автоматически найдет подходящие классы, и будет в соответствующие моменты применять их элементу, над которым производится операция.

3. Если JavaScript-описание перехода не найдено, и в таблице стилей нет подходящих классов, тогда вставка/изъятие элемента происходит как можно быстрее (в следующем фрейме).

<p class="tip">Переходы срабатывают только в том случае, если DOM-операция была инициирована через Vue, либо посредством директивы (например `v-if`), либо посредством вызова одного из методов экземпляра (например `vm.$appendTo()`.</p>

## CSS Переходы

Типичный пример использования CSS-перехода:

``` html
<div v-if="show" v-transition="expand">hello</div>
```

Для этого должны быть описаны следующие классы: `.expand-transition`, `.expand-enter` и `.expand-leave`, например:

``` css
.expand-transition {
  transition: all .3s ease;
  height: 30px;
  padding: 10px;
  background-color: #eee;
  overflow: hidden;
}
.expand-enter, .expand-leave {
  height: 0;
  padding: 0 10px;
  opacity: 0;
}
```

В дополнение к этому можно предоставить функции-хуки:

``` js
Vue.transition('expand', {

  beforeEnter: function (el) {
    el.textContent = 'beforeEnter'
  },
  enter: function (el) {
    el.textContent = 'enter'
  },
  afterEnter: function (el) {
    el.textContent = 'afterEnter'
  },
  enterCancelled: function (el) {
    // handle cancellation
  },

  beforeLeave: function (el) {
    el.textContent = 'beforeLeave'
  },
  leave: function (el) {
    el.textContent = 'leave'
  },
  afterLeave: function (el) {
    el.textContent = 'afterLeave'
  },
  leaveCancelled: function (el) {
    // handle cancellation
  }
})
```

<div id="demo"><div v-if="show" v-transition="expand">Привет!</div><button v-on="click: show = !show">Показать/скрыть</button></div>

<style>
.expand-transition {
  transition: all .3s ease;
  height: 30px;
  padding: 10px;
  background-color: #eee;
  overflow: hidden;
}
.expand-enter, .expand-leave {
  height: 0;
  padding: 0 10px;
  opacity: 0;
}
</style>

<script>
new Vue({
  el: '#demo',
  data: {
    show: true,
    transitionState: 'Idle'
  },
  transitions: {
    expand: {
      beforeEnter: function (el) {
        el.textContent = 'beforeEnter'
      },
      enter: function (el) {
        el.textContent = 'enter'
      },
      afterEnter: function (el) {
        el.textContent = 'afterEnter'
      },
      beforeLeave: function (el) {
        el.textContent = 'beforeLeave'
      },
      leave: function (el) {
        el.textContent = 'leave'
      },
      afterLeave: function (el) {
        el.textContent = 'afterLeave'
      }
    }
  }
})
</script>

Значение директивы `v-transition` влияет на то, какие классы будут применяться к элементу. Например, в случае с `v-transition="fade"`, класс `.fade-transition` будет присутствовать всегда, а классы `.fade-enter` и `.fade-leave` будут автоматически применяться и отменяться в нужные моменты. Если директиве `v-transition` не передавать значение, тогда применяться будут классы `.v-transition`, `.v-enter` и `.v-leave` соответственно.

Когда свойство `show` в примере выше изменяется, Vue вставит/изымет элемент `<div>` из дерева, применив соответствующие классы и вызвав соответствующие функции-хуки в определенном порядке:

- Когда `show` примет значение `false`, Vue:
  1. Вызовет хук `beforeLeave`;
  2. Применит класс `v-leave` к элементу, инициировав тем самым CSS-переход;
  3. Вызовет хук `leave`;
  4. Дождется окончания CSS-перехода (обозначается событием `transitionend`);
  5. Уберет элемент из дерева DOM, и отменит класс `v-leave`;
  6. Вызовет хук `afterLeave`.

- Когда `show` примет значение `true`, Vue:
  1. Вызовет хук `beforeEnter`;
  2. Применит класс `v-enter` к элементу;
  3. Вставит элемент в дерево DOM;
  4. Вызовет хук `enter`;
  5. Спровоцирует перерасчет стилей, чтобы класс `v-enter` фактически применился, затем уберет класс `v-enter` чтобы инициировать CSS-переход элемента в обычное состояние;
  6. Дождется окончания CSS-перехода;
  7. Вызовет хук `afterEnter`.

В дополнение к вышеприведенным хукам, если элемент убрать до того, как анимация его появления завершится, то будет вызван хук `enterCancelled`, в котором необходимо освободить зарезервированные в хуке `enter` ресурсы (например таймеры). Аналогично для прерванной анимации исчезания существует хук `leaveCanceled`.

Все вышеприведенные хуки вызываются в контексте соответствующего экземпляра Vue. Если анимируемый элемент является корневым элементом экземпляра, то this внутри хука будет указывает на этот экземпляр. В противном случае, this будет указывать на экземпляр, являющийся владельцем элемента с директивой.

Наконец, хуки `enter` и `leave` могут дополнительно принимать функцию обратного вызова вторым аргументом. Если хук принимает второй аргумент, то это подсказывает Vue, что вы намерены самостоятельно управлять анимацией перехода, и вместо ожидания события `transitionend` Vue будет ожидать вызова этой функции. Например:

``` js
enter: function (el) {
  // второго аргумента нет, окончание перехода
  // определяется событием `transitionend`
}
```

либо

``` js
enter: function (el, done) {
  // второй аргумент присутствует, переход завершится
  // когда будет вызвана функция `done`
}
```

<p class="tip">Когда несколько элементов совершают переход одновременно, Vue совершает только один перерасчет стилей.</p>

## CSS Анимации

CSS-анимации применяются почти так же, как CSS-переходы. Разница лишь в том, что класс `v-enter` отменяется не сразу после вставки элемента в DOM дерево, а по событию `animationend`.

**Пример:** (браузерные префиксы опущены для краткости)

``` html
<span v-show="show" v-transition="bounce">Вот он я!</span>
```

``` css
.bounce-enter {
  animation: bounce-in .5s;
}
.bounce-leave {
  animation: bounce-out .5s;
}
@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(1);
  }
}
@keyframes bounce-out {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(0);
  }
}
```

<div id="anim" class="demo"><span v-show="show" v-transition="bounce">Вот он я!</span><br><button v-on="click: show = !show">Показать/скрыть</button></div>

<style>
  .bounce-enter {
    -webkit-animation: bounce-in .5s;
    animation: bounce-in .5s;
  }
  .bounce-leave {
    -webkit-animation: bounce-out .5s;
    animation: bounce-out .5s;
  }
  @keyframes bounce-in {
    0% {
      transform: scale(0);
      -webkit-transform: scale(0);
    }
    50% {
      transform: scale(1.5);
      -webkit-transform: scale(1.5);
    }
    100% {
      transform: scale(1);
      -webkit-transform: scale(1);
    }
  }
  @keyframes bounce-out {
    0% {
      transform: scale(1);
      -webkit-transform: scale(1);
    }
    50% {
      transform: scale(1.5);
      -webkit-transform: scale(1.5);
    }
    100% {
      transform: scale(0);
      -webkit-transform: scale(0);
    }
  }
  @-webkit-keyframes bounce-in {
    0% {
      -webkit-transform: scale(0);
    }
    50% {
      -webkit-transform: scale(1.5);
    }
    100% {
      -webkit-transform: scale(1);
    }
  }
  @-webkit-keyframes bounce-out {
    0% {
      -webkit-transform: scale(1);
    }
    50% {
      -webkit-transform: scale(1.5);
    }
    100% {
      -webkit-transform: scale(0);
    }
  }
</style>

<script>
new Vue({
  el: '#anim',
  data: { show: true }
})
</script>

## Чистые JavaScript-переходы

При желании можно ограничиться лишь JavaScript-переходами и не определять никаких классов. При таком подходе второй аргумент `done` для хуков `enter` и `leave` обязателен, иначе они будут вызваны синхронно и переход закончится немедленно. В приведенном ниже примере пользовательский переход реализован с помощью jQuery, и регистрируется глобально для всего приложения.

``` js
Vue.transition('fade', {
  enter: function (el, done) {
    // элемент уже вставлен в дерево DOM,
    // нужно вызвать `done` когда анимация завершится
    $(el)
      .css('opacity', 0)
      .animate({ opacity: 1 }, 1000, done)
  },
  enterCancelled: function (el) {
    $(el).stop()
  },
  leave: function (el, done) {
    // аналогично `enter`
    $(el).animate({ opacity: 0 }, 1000, done)
  },
  leaveCancelled: function (el) {
    $(el).stop()
  }
})
```

После регистрации такой JavaScript-переход можно использовать указав его имя в `v-transition`.

``` html
<p v-transition="fade"></p>
```

<p class="tip">Если к элементу, использующему чистый JavaScript-переход, тем или иным образом применяется CSS-переход или CSS-анимация, то это может помешать правильной работе перехода. Чтобы избежать подобных проблем можно указать `css: false` в описании JavaScript-перехода. Тогда Vue не будет пытаться найти подходящий CSS-переход.</p>

## Поочередная анимация

Используя `v-transition` вместе с `v-repeat` можно добиться эффекта поочередной анимации. Для этого нужно добавить атрибут `stagger`, `enter-stagger` или `leave-stagger` к элементу, совершающему переход.

``` html
<div v-repeat="list" v-transition stagger="100"></div>
```

Либо можно описать хук `stagger`, `enterStagger` или `leaveStagger`, это дает больше свободы.

``` js
Vue.transition('stagger', {
  stagger: function (index) {
    // увеличивать задержку на 50мс для каждого
    // последующего элемента, но не более 300мс
    return Math.min(300, index * 50)
  }
})
```

Пример:

<iframe width="100%" height="200" style="margin-left:10px" src="http://jsfiddle.net/yyx990803/ujqrsu6w/embedded/result,html,js,css" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

Далее: [Building Larger Apps](/guide/application.html).



*[Поочередная анимация]: Staggering animation
*[перерасчет стилей]: CSS layout
*[чистый JavaScript-переход]: JavaScript-only transition
*[Чистые JavaScript-переходы]: JavaScript Only Transitions
*[функцию обратного вызова]: callback
