# Quy tắc ưu tiên mức "D": Thận trọng khi dùng {#priority-d-rules-use-with-caution}

Một vài tính năng của Vue tồn tại để phục vụ cho các trường hợp đặc biệt hoặc để dễ dàng chuyển đổi từ một codebase cũ (Có thể từ Vue 2). Tuy nhiên, khi sử dụng quá nhiều, chúng có thể làm cho code của bạn khó bảo trì hơn hoặc thậm chí nó sẽ trở thành lỗi trong tương lai. Những quy tắc này sẽ giúp bạn nhận ra những tính năng có thể gây rủi ro, mô tả khi nào và tại sao chúng nên được tránh sử dụng.

## Truy cập phần tử HTML với `scoped` {#element-selectors-with-scoped}

**Nên tránh việc truy cập (select) phần tử HTML với `scoped`**

Nên ưu tiên việc sử dụng css class hơn là truy cập element từ `scoped` (trong `<styles scoped>`), bởi vì số lượng lớn các element sẽ làm giảm hiệu năng.

::: details Giải thích cụ thể
Để tạo sự độc nhất cho các style, Vue sẽ thêm một thuộc tính (attribute) duy nhất vào các phần tử của component, ví dụ như `<button data-v-f3f3eg9>`. Sau đó chúng ta thường truy cập vào các phần tử HTML bằng chính các thuộc tính đó (ví dụ như `button[data-v-f3f3eg9]`).

Vấn đề là khi có một lượng lớn các phần tử HTML được truy cập bằng thuộc tính (ví dụ như `button[data-v-f3f3eg9]`) sẽ chậm hơn rất nhiều so với sử dụng class (ví dụ như `.btn-close[data-v-f3f3eg9]`), vì vậy chúng ta nên ưu tiên sử dụng class hơn khi có thể.
:::

<div class="style-example style-example-bad">
<h3>Không nên</h3>

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
<h3>Nên</h3>

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

## Giao tiếp ngầm giữa cha và con {#implicit-parent-child-communication}

**Giao tiếp giữa các component cha và con nên được thực hiện bằng prop và sự kiện thay vì sử dụng `this.$parent` hoặc thay đổi prop.**

Một ứng dụng Vue lí tưởng tuân thủ quy tắc “props down, events up” (thay đổi thuộc tính từ trên xuống, bắn sự kiện từ dưới lên). Việc này giúp component của bạn rõ ràng hơn. Tuy nhiên, cũng có những trường hợp ngoại lệ, mà trong đó thay đổi prop hoặc sử dụng `this.$parent` có thể đơn giản hóa hai component đang gắn kết chặt chẽ với nhau.

Vấn đề là, cũng có rất nhiều trường hợp *đơn giản* mà những cách làm này tỏ ra rất tiện lợi. Hãy cẩn thận: đừng đánh đổi sự đơn giản (khả năng đọc hiểu flow của trạng thái) để lấy sự tiện lợi ngắn hạn (viết ít code) này.

<div class="style-example style-example-bad">
<h3>Không nên</h3>

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
<h3>Nên</h3>

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
