# Quy tắc ưu tiên "D": Sử dụng 1 cách cẩn thận {#priority-d-rules-use-with-caution}

Một vài tính năng của Vue tồn tại để phục vụ cho các trường hợp đặc biệt hoặc để dễ dàng chuyển đổi từ một codebase cũ (Có thể từ Vue 2). Tuy nhiên, khi sử dụng quá nhiều, chúng có thể làm cho code của bạn khó bảo trì hơn hoặc thậm chí nó sẽ trở thành lỗi trong tương lai. Những quy tắc này sẽ giúp bạn nhận ra những tính năng có thể gây rủi ro, mô tả khi nào và tại sao chúng nên được tránh sử dụng.

## Truy cập phần tử HTML với `scoped` {#element-selectors-with-scoped}

**Nên tránh việc truy cập (select) phần tử HTML với `scoped`**

Nên ưu tiên việc sử dụng css class hơn là truy cập element từ `scoped` (trong `<styles scoped>`), bởi vì số lượng lớn các element sẽ làm giảm hiệu năng.

::: details Giải thích cụ thể
Để tạo sự độc nhất cho các style, Vue sẽ thêm một thuộc tính (attribute) duy nhất vào các phần tử của component, ví dụ như `<button data-v-f3f3eg9>`. Sau đó chúng ta thường truy cập vào các phần tử HTML bằng chính các thuộc tính đó (ví dụ như `button[data-v-f3f3eg9]`).

Vấn đề là khi có một lượng lớn các phần tử HTML được truy cập bằng thuộc tính (ví dụ như `button[data-v-f3f3eg9]`) sẽ chậm hơn rất nhiều so với sử dụng class (ví dụ như `.btn-close[data-v-f3f3eg9]`), vì vậy chúng ta nên ưu tiên sử dụng class hơn khi có thể.
:::

<div class="style-example style-example-bad">
<h3>Không khuyên dùng</h3>

```vue-html
<template>
  <button>×</button>
</template>

<style scoped>
button {
  background-color: red;
}
</style>
```

</div>

<div class="style-example style-example-good">
<h3>Khuyên dùng</h3>

```vue-html
<template>
  <button class="btn btn-close">×</button>
</template>

<style scoped>
.btn-close {
  background-color: red;
}
</style>
```

</div>

## Implicit parent-child communication {#implicit-parent-child-communication}

**Props and events should be preferred for parent-child component communication, instead of `this.$parent` or mutating props.**

An ideal Vue application is props down, events up. Sticking to this convention makes your components much easier to understand. However, there are edge cases where prop mutation or `this.$parent` can simplify two components that are already deeply coupled.

The problem is, there are also many _simple_ cases where these patterns may offer convenience. Beware: do not be seduced into trading simplicity (being able to understand the flow of your state) for short-term convenience (writing less code).

<div class="style-example style-example-bad">
<h3>Bad</h3>

```js
app.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },

  template: '<input v-model="todo.text">'
})
```

```js
app.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },

  methods: {
    removeTodo() {
      this.$parent.todos = this.$parent.todos.filter(
        (todo) => todo.id !== vm.todo.id
      )
    }
  },

  template: `
    <span>
      {{ todo.text }}
      <button @click="removeTodo">
        ×
      </button>
    </span>
  `
})
```

</div>

<div class="style-example style-example-good">
<h3>Good</h3>

```js
app.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },

  emits: ['input'],

  template: `
    <input
      :value="todo.text"
      @input="$emit('input', $event.target.value)"
    >
  `
})
```

```js
app.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },

  emits: ['delete'],

  template: `
    <span>
      {{ todo.text }}
      <button @click="$emit('delete')">
        ×
      </button>
    </span>
  `
})
```

</div>
