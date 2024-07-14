# Render các danh sách {#list-rendering}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/list-rendering-in-vue-3" title="Free Vue.js List Rendering Lesson"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-list-rendering-in-vue" title="Free Vue.js List Rendering Lesson"/>
</div>

## `v-for` {#v-for}

Chúng ta có thể sử dụng directive `v-for` để render một danh sách các item dựa trên một mảng. Directive `v-for` yêu cầu một cú pháp đặc biệt theo dạng `item in items`, trong đó `items` là mảng dữ liệu nguồn và `item` là **alias** cho phần tử mảng đang được lặp:

<div class="composition-api">

```js
const items = ref([{ message: 'Foo' }, { message: 'Bar' }])
```

</div>

<div class="options-api">

```js
data() {
  return {
    items: [{ message: 'Foo' }, { message: 'Bar' }]
  }
}
```

</div>

```vue-html
<li v-for="item in items">
  {{ item.message }}
</li>
```

Bên trong scope của `v-for`, các biểu thức template có thể truy cập tất cả các thuộc tính của scope cha. Ngoài ra, `v-for` cũng hỗ trợ một alias thứ hai tùy chọn cho index của item hiện tại:

<div class="composition-api">

```js
const parentMessage = ref('Message Cha')
const items = ref([{ message: 'Foo' }, { message: 'Bar' }])
```

</div>
<div class="options-api">

```js
data() {
  return {
    parentMessage: 'Message Cha',
    items: [{ message: 'Foo' }, { message: 'Bar' }]
  }
}
```

</div>

```vue-html
<li v-for="(item, index) in items">
  {{ parentMessage }} - {{ index }} - {{ item.message }}
</li>
```

<script setup>
const parentMessage = 'Message Cha'
const items = [{ message: 'Foo' }, { message: 'Bar' }]
</script>
<div class="demo">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</div>

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNpdTsuqwjAQ/ZVDNlFQu5d64bpwJ7g3LopOJdAmIRlFCPl3p60PcDWcM+eV1X8Iq/uN1FrV6RxtYCTiW/gzzvbBR0ZGpBYFbfQ9tEi1ccadvUuM0ERyvKeUmithMyhn+jCSev4WWaY+vZ7HjH5Sr6F33muUhTR8uW0ThTuJua6mPbJEgGSErmEaENedxX3Z+rgxajbEL2DdhR5zOVOdUSIEDOf8M7IULCHsaPgiMa1eK4QcS6rOSkhdfapVeQLQEWnH)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNpVTssKwjAQ/JUllyr0cS9V0IM3wbvxEOxWAm0a0m0phPy7m1aqhpDsDLMz48XJ2nwaUZSiGp5OWzpKg7PtHUGNjRpbAi8NQK1I7fbrLMkhjc5EJAn4WOXQ0BWHQb2whOS24CSN6qjXhN1Qwt1Dt2kufZ9ASOGXOyvH3GMNCdGdH75VsZVjwGa2VYQRUdVqmLKmdwcpdjEnBW1qnPf8wZIrBQujoff/RSEEyIDZZeGLeCn/dGJyCSlazSZVsUWL8AYme21i)

</div>

Biến phạm vi của `v-for` tương tự như đoạn JavaScript sau:

```js
const parentMessage = 'Message Cha'
const items = [
  /* ... */
]

items.forEach((item, index) => {
  // có thể truy cập `parentMessage` ở phạm vi bên ngoài
  // nhưng `item` và `index` chỉ có thể truy cập trong phạm vi này
  console.log(parentMessage, item.message, index)
})
```

Chú ý cách giá trị của `v-for` khớp với chữ ký hàm callback của `forEach`. Thực tế, bạn có thể sử dụng destructuring trên alias của `v-for` tương tự như destructuring đối số của hàm:

```vue-html
<li v-for="{ message } in items">
  {{ message }}
</li>

<!-- with index alias -->
<li v-for="({ message }, index) in items">
  {{ message }} {{ index }}
</li>
```

Đối với `v-for` lồng nhau, phạm vi cũng hoạt động tương tự như các hàm lồng nhau trong JavaScript. Mỗi phạm vi `v-for` có quyền truy cập vào phạm vi cha:

```vue-html
<li v-for="item in items">
  <span v-for="childItem in item.children">
    {{ item.message }} {{ childItem }}
  </span>
</li>
```

Bạn cũng có thể sử dụng `of` thay vì `in` làm phân tách, để nó gần với cú pháp của JavaScript cho iterators:

```vue-html
<div v-for="item of items"></div>
```

## `v-for` với một Object {#v-for-with-an-object}

Bạn cũng có thể sử dụng `v-for` để lặp qua các thuộc tính của một object. Để có thể truy cập vào các giá trị của thuộc tính, chúng ta có thể sử dụng tham chiếu đến thuộc tính của object:

<div class="composition-api">

```js
const myObject = reactive({
  title: 'Làm sao để xử lí danh sách trong Vue',
  author: 'Jane Doe',
  publishedAt: '2016-04-10'
})
```

</div>
<div class="options-api">

```js
data() {
  return {
    myObject: {
      title: 'Làm sao để xử lí danh sách trong Vue',
      author: 'Jane Doe',
      publishedAt: '2016-04-10'
    }
  }
}
```

</div>

```vue-html
<ul>
  <li v-for="value in myObject">
    {{ value }}
  </li>
</ul>
```

Bạn cũng có thể cung cấp một bí danh thứ hai cho tên thuộc tính (a.k.a. key):

```vue-html
<li v-for="(value, key) in myObject">
  {{ key }}: {{ value }}
</li>
```

Và một bí danh thứ ba cho index:

```vue-html
<li v-for="(value, key, index) in myObject">
  {{ index }}. {{ key }}: {{ value }}
</li>
```

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNo9jjFvgzAQhf/KE0sSCQKpqg7IqRSpQ9WlWycvBC6KW2NbcKaNEP+9B7Tx4nt33917Y3IKYT9ESspE9XVnAqMnjuFZO9MG3zFGdFTVbAbChEvnW2yE32inXe1dz2hv7+dPqhnHO7kdtQPYsKUSm1f/DfZoPKzpuYdx+JAL6cxUka++E+itcoQX/9cO8SzslZoTy+yhODxlxWN2KMR22mmn8jWrpBTB1AZbMc2KVbTyQ56yBkN28d1RJ9uhspFSfNEtFf+GfnZzjP/oOll2NQPjuM4xTftZyIaU5VwuN0SsqMqtWZxUvliq/J4jmX4BTCp08A==)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNo9T8FqwzAM/RWRS1pImnSMHYI3KOwwdtltJ1/cRqXe3Ng4ctYS8u+TbVJjLD3rPelpLg7O7aaARVeI8eS1ozc54M1ZT9DjWQVDMMsBoFekNtucS/JIwQ8RSQI+1/vX8QdP1K2E+EmaDHZQftg/IAu9BaNHGkEP8B2wrFYxgAp0sZ6pn2pAeLepmEuSXDiy7oL9gduXT+3+pW6f631bZoqkJY/kkB6+onnswoDw6owijIhEMByjUBgNU322/lUWm0mZgBX84r1ifz3ettHmupYskjbanedch2XZRcAKTnnvGVIPBpkqGqPTJNGkkaJ5+CiWf4KkfBs=)

</div>

## `v-for` với một Khoảng {#v-for-with-a-range}

`v-for` cũng có thể lặp qua một khoảng số. Trong trường hợp này nó sẽ lặp lại template một số lần, dựa trên một khoảng số `1...n`:

```vue-html
<span v-for="n in 10">{{ n }}</span>
```

Lưu rằng `n` ở đây bắt đầu với giá trị `1` thay vì `0`.

## `v-for` trong `<template>` {#v-for-on-template}

Tương tự với template `v-if`, bạn cũng có thể sử dụng một thẻ `<template>` với `v-for` để render một block của nhiều elements. Ví dụ:

```vue-html
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider" role="presentation"></li>
  </template>
</ul>
```

## `v-for` with `v-if` {#v-for-with-v-if}

:::warning Lưu ý
**Không** nên sử dụng `v-if` và `v-for` trên cùng một element. Đọc thêm tại [style guide](/style-guide/rules-essential#avoid-v-if-with-v-for).
:::

Khi sử dụng cùng một element với `v-if` và `v-for`, `v-if` có độ ưu tiên cao hơn. Điều đó có nghĩa là `v-if` sẽ không có quyền truy cập vào các biến trong scope của `v-for`:

```vue-html
<!--
Đoạn mã này sẽ bị lỗi vì property "todo" không được định nghĩa trên instance.
-->
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo.name }}
</li>
```

Việc này có thể được khắc phục bằng cách di chuyển `v-for` vào một thẻ `<template>` bọc bên ngoài (điều này cũng khiến code rõ ràng hơn):

```vue-html
<template v-for="todo in todos">
  <li v-if="!todo.isComplete">
    {{ todo.name }}
  </li>
</template>
```

## Bảo trì trạng thái với `key` {#maintaining-state-with-key}

Khi Vue đang cập nhật một danh sách các element được render với `v-for`, nó sử dụng mặc định một chiến lược "in-place patch". Nếu thứ tự của các item dữ liệu đã thay đổi, thay vì di chuyển các element DOM để phù hợp với thứ tự của các item, Vue sẽ patch từng element tại chỗ và đảm bảo nó phản ánh những gì nên được render tại chỉ số cụ thể đó.

Chế độ mặc định này hiệu quả, nhưng **chỉ phù hợp khi output của danh sách render không phụ thuộc vào trạng thái của các component con hoặc trạng thái DOM tạm thời (ví dụ: giá trị của input form)**.

Để gợi ý cho Vue biết cách theo dõi các node để nó có thể tái sử dụng và sắp xếp lại các element hiện có, bạn cần cung cấp một thuộc tính `key` duy nhất cho mỗi item. Một ví dụ đơn giản như sau:

```vue-html
<div v-for="item in items" :key="item.id">
  <!-- nội dung -->
</div>
```

Khi sử dụng `<template v-for>`, `key` nên được đặt trên container `<template>`:

```vue-html
<template v-for="todo in todos" :key="todo.name">
  <li>{{ todo.name }}</li>
</template>
```

:::tip Lưu ý
`key` ở đây là một thuộc tính đặc biệt được gắn với `v-bind`. Nó không nên bị nhầm lẫn với biến key của object khi [sử dụng `v-for` với một object](#v-for-with-an-object).
:::

[Khuyến khích việc cung cấp một thuộc tính](/style-guide/rules-essential#use-keyed-v-for) `key` với `v-for` nếu có thể, trừ khi nội dung DOM được lặp lại là đơn giản (ví dụ: không chứa các component hoặc các element DOM có trạng thái), hoặc bạn đang cố ý sử dụng hành vi mặc định để tăng hiệu suất.

Ràng buộc `key` thường sử dụng các giá trị nguyên thuỷ - ví dụ như string hoặc number. Không được sử dụng object hoặc mảng như là các giá trị `key`. Để biết thêm chi tiết về cách sử dụng thuộc tính `key`, hãy xem [API documentation](/api/built-in-directives#v-for).

## `v-for` với Component {#v-for-with-a-component}

> Phần này giả định bạn đã biết về [Components](/guide/essentials/component-basics). Bạn có thể bỏ qua nó và quay lại sau.

Bạn có thể sử dụng `v-for` trên một component, giống như bất kỳ element thông thường nào (đừng quên cung cấp một `key`):

```vue-html
<MyComponent v-for="item in items" :key="item.id" />
```

Tuy nhiên, điều này sẽ không tự động truyền bất kỳ dữ liệu nào vào component, vì component có phạm vi cô lập riêng của chúng. Để truyền dữ liệu được lặp vào component, chúng ta cũng nên sử dụng props:

```vue-html
<MyComponent
  v-for="(item, index) in items"
  :item="item"
  :index="index"
  :key="item.id"
/>
```

Lý do cho việc không tự động truyền dữ liệu vào component là vì nó làm cho component bị ràng buộc chặt chẽ với cách `v-for` hoạt động. Việc rõ ràng về nơi dữ liệu được truyền vào component làm cho component có thể tái sử dụng được trong các tình huống khác.

<div class="composition-api">

Xem qua [ví dụ về một todo list cơ bản](https://play.vuejs.org/#eNp1U8Fu2zAM/RXCGGAHTWx02ylwgxZYB+ywYRhyq3dwLGYRYkuCJTsZjPz7KMmK3ay9JBQfH/meKA/Rk1Jp32G0jnJdtVwZ0Gg6tSkEb5RsDQzQ4h4usG9lAzGVxldoK5n8ZrAZsTQLCduRygAKUUmhDQg8WWyLZwMPtmESx4sAGkL0mH6xrMH+AHC2hvuljw03Na4h/iLBHBAY1wfUbsTFVcwoH28o2/KIIDuaQ0TTlvrwNu/TDe+7PDlKXZ6EZxTiN4kuRI3W0dk4u4yUf7bZfScqw6WAkrEf3m+y8AOcw7Qv6w5T1elDMhs7Nbq7e61gdmme60SQAvgfIhExiSSJeeb3SBukAy1D1aVBezL5XrYN9Csp1rrbNdykqsUehXkookl0EVGxlZHX5Q5rIBLhNHFlbRD6xBiUzlOeuZJQz4XqjI+BxjSSYe2pQWwRBZizV01DmsRWeJA1Qzv0Of2TwldE5hZRlVd+FkbuOmOksJLybIwtkmfWqg+7qz47asXpSiaN3lxikSVwwfC8oD+/sEnV+oh/qcxmU85mebepgLjDBD622Mg+oDrVquYVJm7IEu4XoXKTZ1dho3gnmdJhedEymn9ab3ysDPdc4M9WKp28xE5JbB+rzz/Trm3eK3LAu8/E7p2PNzYM/i3ChR7W7L7hsSIvR7L2Aal1EhqTp80vF95sw3WcG7r8A0XaeME=) để xem cách render một danh sách các component sử dụng `v-for`, truyền dữ liệu khác nhau cho mỗi instance.

</div>
<div class="options-api">

Xem qua [ví dụ về một todo list cơ bản](https://play.vuejs.org/#eNqNVE2PmzAQ/SsjVIlEm4C27Qmx0a7UVuqhPVS5lT04eFKsgG2BSVJF+e8d2xhIu10tihR75s2bNx9wiZ60To49RlmUd2UrtNkUUjRatQa2iquvBhvYt6qBOEmDwQbEhQQoJJ4dlOOe9bWBi7WWiuIlStNlcJlYrivr5MywxdIDAVo0fSvDDUDiyeK3eDYZxLGLsI8hI7H9DHeYQuwjeAb3I9gFCFMjUXxSYCoELroKO6fZP17Mf6jev0i1ZQcE1RtHaFrWVW/l+/Ai3zd1clQ1O8k5Uzg+j1HUZePaSFwfvdGhfNIGTaW47bV3Mc6/+zZOfaaslegS18ZE9121mIm0Ep17ynN3N5M8CB4g44AC4Lq8yTFDwAPNcK63kPTL03HR6EKboWtm0N5MvldtA8e1klnX7xphEt3ikTbpoYimsoqIwJY0r9kOa6Ag8lPeta2PvE+cA3M7k6cOEvBC6n7UfVw3imPtQ8eiouAW/IY0mElsiZWqOdqkn5NfCXxB5G6SJRvj05By1xujpJWUp8PZevLUluqP/ajPploLasmk0Re3sJ4VCMnxvKQ//0JMqrID/iaYtSaCz+xudsHjLpPzscVGHYO3SzpdixIXLskK7pcBucnTUdgg3kkmcxhetIrmH4ebr8m/n4jC6FZp+z7HTlLsVx1p4M7odcXPr6+Lnb8YOne5+C2F6/D6DH2Hx5JqOlCJ7yz7IlBTbZsf7vjXVBzjvLDrH5T0lgo=) để xem cách render một danh sách các component sử dụng `v-for`, truyền dữ liệu khác nhau cho mỗi instance.

</div>

## Kiểm tra thay đổi trong mảng {#array-change-detection}

### Các phương thức biến đổi {#mutation-methods}

Vue có thể phát hiện khi các phương thức thay đổi mảng được gọi và kích hoạt các cập nhật cần thiết. Các phương thức thay đổi này là:

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

### Thay thế mảng {#replacing-an-array}

Các phương thức biến đổi, như tên gọi của chúng, sẽ thay đổi mảng gốc. Ngoài ra, cũng có các phương thức không biến đổi, ví dụ như `filter()`, `concat()` và `slice()`. Các phương thức này không thay đổi mảng gốc mà **luôn luôn trả về một mảng mới**. Khi làm việc với các phương thức không biến đổi, chúng ta nên thay thế mảng cũ bằng mảng mới:

<div class="composition-api">

```js
// `items` là một ref với value là mảng
items.value = items.value.filter((item) => item.message.match(/Foo/))
```

</div>
<div class="options-api">

```js
this.items = this.items.filter((item) => item.message.match(/Foo/))
```

</div>

Bạn có thể nghĩ rằng điều này sẽ khiến Vue phải vứt bỏ DOM hiện tại và render lại toàn bộ danh sách - may mắn thay, điều đó không phải là sự thật. Vue sử dụng một số hướng giải quyết thông minh để tối đa hóa việc tái sử dụng các phần tử DOM hiện có, vì vậy việc thay thế một mảng bằng một mảng khác chứa các đối tượng trùng lặp là một hoạt động rất hiệu quả.

## Hiển thị kết quả đã lọc/sắp xếp {#displaying-filtered-sorted-results}

Đôi khi chúng ta muốn hiển thị một phiên bản đã lọc hoặc sắp xếp của một mảng mà không thực sự thay đổi hoặc đặt lại dữ liệu gốc. Trong trường hợp này, bạn có thể tạo một computed property trả về mảng đã lọc hoặc sắp xếp.

Ví dụ:

<div class="composition-api">

```js
const numbers = ref([1, 2, 3, 4, 5])

const evenNumbers = computed(() => {
  return numbers.value.filter((n) => n % 2 === 0)
})
```

</div>
<div class="options-api">

```js
data() {
  return {
    numbers: [1, 2, 3, 4, 5]
  }
},
computed: {
  evenNumbers() {
    return this.numbers.filter(n => n % 2 === 0)
  }
}
```

</div>

```vue-html
<li v-for="n in evenNumbers">{{ n }}</li>
```

Trong các trường hợp mà thuộc tính computed không khả thi (ví dụ: bên trong vòng lặp `v-for` lồng nhau), bạn có thể sử dụng một method:

<div class="composition-api">

```js
const sets = ref([
  [1, 2, 3, 4, 5],
  [6, 7, 8, 9, 10]
])

function even(numbers) {
  return numbers.filter((number) => number % 2 === 0)
}
```

</div>
<div class="options-api">

```js
data() {
  return {
    sets: [[ 1, 2, 3, 4, 5 ], [6, 7, 8, 9, 10]]
  }
},
methods: {
  even(numbers) {
    return numbers.filter(number => number % 2 === 0)
  }
}
```

</div>

```vue-html
<ul v-for="numbers in sets">
  <li v-for="n in even(numbers)">{{ n }}</li>
</ul>
```

Hãy cẩn thận với `reverse()` và `sort()` trong thuộc tính computed! Hai phương thức này sẽ thay đổi mảng gốc, điều này nên được tránh trong computed property. Tạo một bản sao của mảng gốc trước khi gọi các phương thức này:

```diff
- return numbers.reverse()
+ return [...numbers].reverse()
```
