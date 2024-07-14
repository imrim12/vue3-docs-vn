# Render theo điều kiện {#conditional-rendering}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/conditional-rendering-in-vue-3" title="Free Vue.js Conditional Rendering Lesson"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-conditionals-in-vue" title="Free Vue.js Conditional Rendering Lesson"/>
</div>

<script setup>
import { ref } from 'vue'
const awesome = ref(true)
</script>

## `v-if` {#v-if}

Directive `v-if` được sử dụng để render một block theo điều kiện. Block chỉ được render nếu biểu thức của directive trả về giá trị truthy.

```vue-html
<h1 v-if="awesome">Vue thật tuyệt vời!</h1>
```

## `v-else` {#v-else}

Bạn có thể sử dụng directive `v-else` để chỉ định một "else block" cho `v-if`:

You can use the `v-else` directive to indicate an "else block" for `v-if`:

```vue-html
<button @click="awesome = !awesome">Toggle</button>

<h1 v-if="awesome">Vue thật tuyệt vời!</h1>
<h1 v-else>Ôi không 😢</h1>
```

<div class="demo">
  <button @click="awesome = !awesome">Toggle</button>
  <h1 v-if="awesome">Vue thật tuyệt vời!</h1>
  <h1 v-else>Ôi không 😢</h1>
</div>

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNpFjkEOgjAQRa8ydIMulLA1hegJ3LnqBskAjdA27RQXhHu4M/GEHsEiKLv5mfdf/sBOxux7j+zAuCutNAQOyZtcKNkZbQkGsFjBCJXVHcQBjYUSqtTKERR3dLpDyCZmQ9bjViiezKKgCIGwM21BGBIAv3oireBYtrK8ZYKtgmg5BctJ13WLPJnhr0YQb1Lod7JaS4G8eATpfjMinjTphC8wtg7zcwNKw/v5eC1fnvwnsfEDwaha7w==)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNpFjj0OwjAMha9iMsEAFWuVVnACNqYsoXV/RJpEqVOQqt6DDYkTcgRSWoplWX7y56fXs6O1u84jixlvM1dbSoXGuzWOIMdCekXQCw2QS5LrzbQLckje6VEJglDyhq1pMAZyHidkGG9hhObRYh0EYWOVJAwKgF88kdFwyFSdXRPBZidIYDWvgqVkylIhjyb4ayOIV3votnXxfwrk2SPU7S/PikfVfsRnGFWL6akCbeD9fLzmK4+WSGz4AA5dYQY=)

</div>

Một element `v-else` phải theo ngay sau một element `v-if` hoặc `v-else-if` - nếu không nó sẽ không được nhận dạng.

## `v-else-if` {#v-else-if}

`v-else-if`, như tên gọi của nó, được sử dụng như một "else if block" cho `v-if`. Nó cũng có thể được sử dụng nhiều lần:

```vue-html
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Không phải A/B/C
</div>
```

Tương tự như `v-else`, một element `v-else-if` phải theo ngay sau một element `v-if` hoặc `v-else-if` - nếu không nó sẽ không được nhận dạng.

## `v-if` on `<template>` {#v-if-on-template}

Bởi vì `v-if` là một directive, nó phải được gắn vào một element duy nhất. Nhưng nếu chúng ta muốn toggle nhiều hơn một element thì sao? Trong trường hợp này chúng ta có thể sử dụng `v-if` trên element `<template>`, nó sẽ hoạt động như một wrapper ẩn. Kết quả cuối cùng sẽ không bao gồm element `<template>`.

```vue-html
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```

`v-else` và `v-else-if` cũng có thể được sử dụng trên `<template>`:

## `v-show` {#v-show}

Một lựa chọn khác để điều kiện hiển thị một element là directive `v-show`. Cách sử dụng cũng tương tự:

```vue-html
<h1 v-show="ok">Xin chào!</h1>
```

Sự khác biệt đó là một element với `v-show` sẽ luôn được render và vẫn nằm trong DOM; `v-show` chỉ toggle thuộc tính CSS `display` của element.

`v-show` không hỗ trợ `<template>` element, cũng không hoạt động với `v-else`.

## `v-if` vs. `v-show` {#v-if-vs-v-show}

`v-if` là render theo điều kiện "real" vì nó đảm bảo rằng event listener và các component con bên trong block điều kiện được hủy và tạo lại đúng cách trong quá trình toggle.

`v-if` còn mang tính **lazy**: nếu điều kiện là false trong lần render ban đầu, nó sẽ không làm gì cả - block điều kiện sẽ không được render cho đến khi điều kiện trở thành true lần đầu tiên.

Trong khi đó, `v-show` là render theo điều kiện "virtual". Nếu điều kiện là false trong lần render ban đầu, nó vẫn sẽ được render, chỉ là ẩn đi. Block điều kiện sẽ luôn được render ngay cả khi điều kiện là false.

Nói chung, `v-if` có chi phí toggle cao hơn trong khi `v-show` có chi phí render ban đầu cao hơn. Vì vậy, nếu bạn cần toggle một cái gì đó rất thường xuyên, hãy sử dụng `v-show`, và nếu điều kiện không thay đổi trong quá trình runtime, hãy sử dụng `v-if`.

## Sử dụng `v-if` cùng với `v-for` {#v-if-with-v-for}

::: warning Lưu ý
**Không** nên sử dụng `v-if` và `v-for` trên cùng một element vì sự mâu thuẫn về ưu tiên ngầm định. Xem [style guide](/style-guide/rules-essential#avoid-v-if-with-v-for) để biết chi tiết.
:::

Khi `v-if` và `v-for` được sử dụng trên cùng một element, `v-if` sẽ được đánh giá trước. Xem [hướng dẫn về list](list#v-for-with-v-if) để biết chi tiết.
