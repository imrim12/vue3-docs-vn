### Ưu tiên mức C: Khuyên dùng {#priority-c-rules-recommended}

Chúng ta có thể tự ý chọn một trong nhiều lựa chọn tốt ngang nhau để bảo đảm tính nhất quán. Trong các quy tắc này, chúng tôi sẽ mô tả từng lựa chọn được chấp nhận và đề nghị chọn một lựa chọn mặc định. Điều này có nghĩa là bạn hoàn toàn có thể chọn một lựa chọn khác cho dự án của mình, miễn là đảm bảo thống nhất và có lí do chính đáng. Việc có lí do chính đáng là rất quan trọng, vì khi tuân thủ theo tiêu chuẩn chung, bạn sẽ:

1. Luyện tập cho não bộ của bạn để dễ dàng đọc code của cộng đồng
2. Có thể copy và paste các ví dụ code từ cộng đồng mà không cần chỉnh sửa
3. Dễ tuyển được người đã quen với phong cách viết code mà bạn muốn, ít nhất là khi dùng Vue

## Thứ tự của các tùy chọn khởi tạo component/instance{#component-instance-options-order}

**Các tuỳ chọn khởi tạo component/instance nên được sắp xếp một cách nhất quán.**

Đây là thứ tự mặc định mà chúng tôi đề nghị cho các tùy chọn khởi tạo component. Chúng được chia thành các nhóm, để bạn biết nên đặt các thuộc tính mới từ các plugin vào đâu.

1. **Global Awareness** (requires knowledge beyond the component)

   - `name`

2. **Template Compiler Options** (changes the way templates are compiled)

   - `compilerOptions`

3. **Template Dependencies** (assets used in the template)

   - `components`
   - `directives`

4. **Composition** (merges properties into the options)

   - `extends`
   - `mixins`
   - `provide`/`inject`

5. **Interface** (the interface to the component)

   - `inheritAttrs`
   - `props`
   - `emits`

6. **Composition API** (the entry point for using the Composition API)

   - `setup`

7. **Local State** (local reactive properties)

   - `data`
   - `computed`

8. **Events** (callbacks triggered by reactive events)

   - `watch`
   - Lifecycle Events (in the order they are called)
     - `beforeCreate`
     - `created`
     - `beforeMount`
     - `mounted`
     - `beforeUpdate`
     - `updated`
     - `activated`
     - `deactivated`
     - `beforeUnmount`
     - `unmounted`
     - `errorCaptured`
     - `renderTracked`
     - `renderTriggered`

9. **Non-Reactive Properties** (instance properties independent of the reactivity system)

   - `methods`

10. **Rendering** (the declarative description of the component output)
    - `template`/`render`

## Element attribute order {#element-attribute-order}

**The attributes of elements (including components) should be ordered consistently.**

This is the default order we recommend for component options. They're split into categories, so you'll know where to add custom attributes and directives.

1. **Definition** (provides the component options)

   - `is`

2. **List Rendering** (creates multiple variations of the same element)

   - `v-for`

3. **Conditionals** (whether the element is rendered/shown)

   - `v-if`
   - `v-else-if`
   - `v-else`
   - `v-show`
   - `v-cloak`

4. **Render Modifiers** (changes the way the element renders)

   - `v-pre`
   - `v-once`

5. **Global Awareness** (requires knowledge beyond the component)

   - `id`

6. **Unique Attributes** (attributes that require unique values)

   - `ref`
   - `key`

7. **Two-Way Binding** (combining binding and events)

   - `v-model`

8. **Other Attributes** (all unspecified bound & unbound attributes)

9. **Events** (component event listeners)

   - `v-on`

10. **Content** (overrides the content of the element)
    - `v-html`
    - `v-text`

## Empty lines in component/instance options {#empty-lines-in-component-instance-options}

**You may want to add one empty line between multi-line properties, particularly if the options can no longer fit on your screen without scrolling.**

When components begin to feel cramped or difficult to read, adding spaces between multi-line properties can make them easier to skim again. In some editors, such as Vim, formatting options like this can also make them easier to navigate with the keyboard.

<div class="style-example style-example-good">
<h3>Nên</h3>

```js
props: {
  value: {
    type: String,
    required: true
  },

  focused: {
    type: Boolean,
    default: false
  },

  label: String,
  icon: String
},

computed: {
  formattedValue() {
    // ...
  },

  inputClasses() {
    // ...
  }
}
```

```js
// No spaces are also fine, as long as the component
// is still easy to read and navigate.
props: {
  value: {
    type: String,
    required: true
  },
  focused: {
    type: Boolean,
    default: false
  },
  label: String,
  icon: String
},
computed: {
  formattedValue() {
    // ...
  },
  inputClasses() {
    // ...
  }
}
```

</div>

## Single-file component top-level element order {#single-file-component-top-level-element-order}

**[Single-File Components](/guide/scaling-up/sfc) should always order `<script>`, `<template>`, and `<style>` tags consistently, with `<style>` last, because at least one of the other two is always necessary.**

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```vue-html
<style>/* ... */</style>
<script>/* ... */</script>
<template>...</template>
```

```vue-html
<!-- ComponentA.vue -->
<script>/* ... */</script>
<template>...</template>
<style>/* ... */</style>

<!-- ComponentB.vue -->
<template>...</template>
<script>/* ... */</script>
<style>/* ... */</style>
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```vue-html
<!-- ComponentA.vue -->
<script>/* ... */</script>
<template>...</template>
<style>/* ... */</style>

<!-- ComponentB.vue -->
<script>/* ... */</script>
<template>...</template>
<style>/* ... */</style>
```

```vue-html
<!-- ComponentA.vue -->
<template>...</template>
<script>/* ... */</script>
<style>/* ... */</style>

<!-- ComponentB.vue -->
<template>...</template>
<script>/* ... */</script>
<style>/* ... */</style>
```

</div>
