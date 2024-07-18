# Quy tắc ưu tiên mức "A": Thiết yếu (Tránh gặp lỗi) {#priority-a-rules-essential}

Các quy tắc này giúp tránh xảy ra lỗi, vì vậy hãy học và làm theo bằng bất cứ giá nào. Ngoại lệ nếu có cũng sẽ rất hiếm, và chỉ có thể xảy ra với những ai có kiến thức sâu sắc về cả JavaScript và Vue.

## Đặt tên component dùng nhiều từ {#use-multi-word-component-names}

Tên component nên dùng hai từ (đơn) trở lên, ngoại trừ component gốc `App.vue`. Việc này giúp [tránh xung đột](https://html.spec.whatwg.org/multipage/custom-elements.html#valid-custom-element-name) với các phần tử HTML hiện tại và tương lai, vì tất cả các phần tử HTML đều là từ đơn.

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```vue-html
<!-- in pre-compiled templates -->
<Item />

<!-- in in-DOM templates -->
<item></item>
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```vue-html
<!-- in pre-compiled templates -->
<TodoItem />

<!-- in in-DOM templates -->
<todo-item></todo-item>
```

</div>

## Định nghĩa cho prop càng chi tiết càng tốt. {#use-detailed-prop-definitions}

Trong code được commit, định nghĩa cho prop nên chi tiết đến mức có thể. Ít nhất bạn phải chỉ định kiểu dữ liệu của prop.

::: details Giải thích cụ thể
[Định nghĩa prop](/guide/components/props#prop-validation) chi tiết có hai lợi ích:

- API của component trở nên rõ ràng như một tài liệu, giúp hiểu dễ dàng hơn về cách dùng component.
- Trong lúc phát triển, Vue sẽ cảnh báo nếu prop được truyền vào không đúng định dạng, giúp bạn phát hiện các lỗi có thể xảy ra.
  :::

<div class="options-api">
<div class="style-example style-example-bad">
<h3>Không nên</h3>

```js
// Như thế này chỉ ổn khi viết prototype
props: ['status']
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```js
props: {
  status: String
}
```

```js
// Càng tốt hơn!
props: {
  status: {
    type: String,
    required: true,

    validator: value => {
      return [
        'syncing',
        'synced',
        'version-conflict',
        'error'
      ].includes(value)
    }
  }
}
```

</div>
</div>

<div class="composition-api">
<div class="style-example style-example-bad">
<h3>Không nên</h3>

```js
// Như thế này chỉ ổn khi viết prototype
const props = defineProps(['status'])
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```js
const props = defineProps({
  status: String
})
```

```js
// Càng tốt hơn!

const props = defineProps({
  status: {
    type: String,
    required: true,

    validator: (value) => {
      return ['syncing', 'synced', 'version-conflict', 'error'].includes(
        value
      )
    }
  }
})
```

</div>
</div>

## Dùng `key` cho `v-for` {#use-keyed-v-for}

`key` cho `v-for` *luôn luôn* là bắt buộc với component, để bảo đảm trạng thái bên trong của component và các component con. Ngay cả đối với các phần tử, việc này cũng giúp bảo đảm hành vi có thể đoán định được, ví dụ [tính bất biến của object](https://bost.ocks.org/mike/constancy/) trong animation.

::: details Giải thích cụ thể
Ví dụ chúng ta có một danh sách todo:

```js
data() {
  return {
    todos: [
      {
        id: 1,
        text: 'Learn to use v-for'
      },
      {
        id: 2,
        text: 'Learn to use key'
      }
    ]
  }
}
```

Sau đó bạn sắp xếp danh sách này theo thứ tự chữ cái. Khi cập nhật DOM, Vue sẽ tối ưu việc render để thực hiện những thay đổi DOM ít tốn kém nhất. Điều này có thể có nghĩa là xóa đi mục todo đầu tiên, rồi thêm nó vào lại ở cuối danh sách.

Vấn đề là, có những trường hợp chúng ta không muốn xóa đi những phần tử mà cuối cùng vẫn sẽ tồn tại trong DOM. Ví dụ, bạn có thể muốn sử dụng `<transition-group>` để animate việc sắp xếp danh sách, hoặc muốn giữ focus nếu phần tử được render là một `<input>`. Trong những trường hợp này, thêm vào một key không trùng lặp cho mỗi item (ví dụ :key="todo.id") sẽ hướng dẫn cho Vue xử lí theo hướng dễ đoán hơn.

Theo kinh nghiệm của chúng tôi, tốt nhất là luôn luôn dùng một key không trùng lặp, sau đó đơn giản là bạn sẽ không bao giờ phải quan tâm đến các trường hợp hiếm gặp nữa. Sau đó, đối với những trường hợp mà hiệu suất liên quan đến tính bất biến của object là không cần thiết, bạn có thể tạo ngoại lệ một cách tuỳ ý.
:::

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```vue-html
<ul>
  <li v-for="todo in todos">
    {{ todo.text }}
  </li>
</ul>
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```vue-html
<ul>
  <li
    v-for="todo in todos"
    :key="todo.id"
  >
    {{ todo.text }}
  </li>
</ul>
```

</div>

## Tránh dùng `v-if` chung với `v-for` {#avoid-v-if-with-v-for}

**Đừng bao giờ dùng `v-if` trên cùng một phần tử với `v-for`.**

Có hai trường hợp thường gặp mà chúng ta có xu hướng làm như vậy:

- Để lọc các item từ một danh sách (ví dụ `v-for="user in users" v-if="user.isActive"`). Trong những trường hợp này, hãy thay thế `users` bằng một computed property trả về danh sách đã được lọc (ví dụ `activeUsers`).

- Để tránh hiển thị một danh sách mà ta muốn giấu (ví dụ `v-for="user in users" v-if="shouldShowUsers"`). Trong những trường hợp này, hãy chuyển `v-if` lên một phần tử cha (ví dụ `ul`, `ol`).

::: details Giải thích cụ thể
Khi Vue xử lí các directive, `v-for` có độ ưu tiên cao hơn `v-if`, vì thế template sau đây:

```vue-html
<ul>
  <li
    v-for="user in users"
    v-if="user.isActive"
    :key="user.id"
  >
    {{ user.name }}
  </li>
</ul>
```

Sẽ bị lỗi, vì directive `v-if` sẽ được đánh giá trước và biến lặp `user` không tồn tại tại thời điểm này.

Điều này có thể được sửa bằng cách lặp qua một computed property, như sau:

```js
computed: {
  activeUsers() {
    return this.users.filter(user => user.isActive)
  }
}
```

```vue-html
<ul>
  <li
    v-for="user in activeUsers"
    :key="user.id"
  >
    {{ user.name }}
  </li>
</ul>
```

Hoặc là, chúng ta có thể dùng `<template>` với `v-for` để bao bọc phần tử `<li>`:

```vue-html
<ul>
  <template v-for="user in users" :key="user.id">
    <li v-if="user.isActive">
      {{ user.name }}
    </li>
  </template>
</ul>
```

:::

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```vue-html
<ul>
  <li
    v-for="user in users"
    v-if="user.isActive"
    :key="user.id"
  >
    {{ user.name }}
  </li>
</ul>
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```vue-html
<ul>
  <li
    v-for="user in activeUsers"
    :key="user.id"
  >
    {{ user.name }}
  </li>
</ul>
```

```vue-html
<ul>
  <template v-for="user in users" :key="user.id">
    <li v-if="user.isActive">
      {{ user.name }}
    </li>
  </template>
</ul>
```

</div>

## Thiết lập phạm vi cho `style` của component {#use-component-scoped-styling}

Đối với các ứng dụng Vue, `style` trong component `App` cấp cao nhất và các component layout có thể có ảnh hưởng toàn cục, nhưng tất cả các component khác luôn luôn nên được thiết lập phạm vi.

Quy tắc này chỉ áp dụng cho [Single-File Components](/guide/scaling-up/sfc). Chúng ta có thể đạt được điều này bằng cách dùng [thuộc tính `scoped`](https://vue-loader.vuejs.org/en/features/scoped-css.html), [CSS modules](https://vue-loader.vuejs.org/en/features/css-modules), một kĩ thuật quản lí CSS dựa trên tên class như [BEM](http://getbem.com/), hoặc một quy ước / thư viện khác. 

**Tuy vậy, các thư viện component nên sử dụng kĩ thuật quản lí CSS thay vì sử dụng thuộc tính `scoped`.**

Điều này giúp cho việc override các style nội bộ được dễ dàng hơn, với các tên class dễ đọc, không cần phải quá đặc trưng nhưng vẫn rất khó tạo ra xung đột.

::: details Giải thích cụ thể
Nếu bạn đang phát triển một dự án lớn, làm việc chung với nhiều lập trình viên, hoặc thỉnh thoảng nhúng HTML/CSS của bên thứ 3 (như Auth0), việc thiết lập phạm vi một cách nhất quán sẽ bảo đảm các style CSS sẽ chỉ được áp dụng cho đúng những component mà ta mong muốn.

Ngoài thuộc tính `scoped`, sử dụng tên class độc nhất cũng giúp đảm bảo CSS của bên thứ ba không ảnh hưởng đến HTML của bạn. Ví dụ, rất nhiều dự án (Bootstrap, Foundation…) sử dụng các tên class như `button`, `btn`, hay `icon`, vì thế cho dù có không dùng một kĩ thuật như BEM, thêm vào một tiền tố riêng cho ứng dụng hoặc component (như `ButtonClose-icon`) cũng sẽ cung cấp thêm một lớp bảo vệ.
:::

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```vue-html
<template>
  <button class="btn btn-close">×</button>
</template>

<style>
.btn-close {
  background-color: red;
}
</style>
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```vue-html
<template>
  <button class="button button-close">×</button>
</template>

<!-- Using the `scoped` attribute -->
<style scoped>
.button {
  border: none;
  border-radius: 2px;
}

.button-close {
  background-color: red;
}
</style>
```

```vue-html
<template>
  <button :class="[$style.button, $style.buttonClose]">×</button>
</template>

<!-- Using CSS modules -->
<style module>
.button {
  border: none;
  border-radius: 2px;
}

.buttonClose {
  background-color: red;
}
</style>
```

```vue-html
<template>
  <button class="c-Button c-Button--close">×</button>
</template>

<!-- Using the BEM convention -->
<style>
.c-Button {
  border: none;
  border-radius: 2px;
}

.c-Button--close {
  background-color: red;
}
</style>
```

</div>
