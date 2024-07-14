# Class and Style Bindings {#class-and-style-bindings}

A common need for data binding is manipulating an element's class list and inline styles. Since `class` and `style` are both attributes, we can use `v-bind` to assign them a string value dynamically, much like with other attributes. However, trying to generate those values using string concatenation can be annoying and error-prone. For this reason, Vue provides special enhancements when `v-bind` is used with `class` and `style`. In addition to strings, the expressions can also evaluate to objects or arrays.

## Ràng buộc HTML Class {#binding-html-classes}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/dynamic-css-classes-with-vue-3" title="Free Vue.js Dynamic CSS Classes Lesson"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-dynamic-css-classes-with-vue" title="Free Vue.js Dynamic CSS Classes Lesson"/>
</div>

### Ràng buộc cho Object {#binding-to-objects}

Chúng ta có thể truyền một object vào `:class` (viết tắt của `v-bind:class`) để chuyển đổi các class động:

```vue-html
<div :class="{ active: isActive }"></div>
```

Cú pháp ở trên có nghĩa là sự hiện diện của class `active` sẽ được xác định bởi [truthiness](https://developer.mozilla.org/en-US/docs/Glossary/Truthy) của thuộc tính dữ liệu `isActive`.

Bạn có thể có nhiều class được bật/tắt bằng cách có nhiều trường trong object. Ngoài ra, chỉ thị `:class` cũng có thể tồn tại cùng với thuộc tính `class` thông thường. Vì vậy, ta có thể có trạng thái sau:

<div class="composition-api">

```js
const isActive = ref(true)
const hasError = ref(false)
```

</div>

<div class="options-api">

```js
data() {
  return {
    isActive: true,
    hasError: false
  }
}
```

</div>

Và template sau:

```vue-html
<div
  class="static"
  :class="{ active: isActive, 'text-danger': hasError }"
></div>
```

Nó sẽ render:

```vue-html
<div class="static active"></div>
```

Khi `isActive` hoặc `hasError` thay đổi, danh sách class sẽ được cập nhật tương ứng. Ví dụ, nếu `hasError` trở thành `true`, danh sách class sẽ trở thành `"static active text-danger"`.

Bound object không nhất thiết phải là inline:

<div class="composition-api">

```js
const classObject = reactive({
  active: true,
  'text-danger': false
})
```

</div>

<div class="options-api">

```js
data() {
  return {
    classObject: {
      active: true,
      'text-danger': false
    }
  }
}
```

</div>

```vue-html
<div :class="classObject"></div>
```

Nó sẽ render:

```vue-html
<div class="active"></div>
```

Chúng ta cũng có thể ràng buộc với một [computed property](./computed) trả về một object. Đây là một pattern phổ biến và mạnh mẽ:

<div class="composition-api">

```js
const isActive = ref(true)
const error = ref(null)

const classObject = computed(() => ({
  active: isActive.value && !error.value,
  'text-danger': error.value && error.value.type === 'fatal'
}))
```

</div>

<div class="options-api">

```js
data() {
  return {
    isActive: true,
    error: null
  }
},
computed: {
  classObject() {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

</div>

```vue-html
<div :class="classObject"></div>
```

### Ràng buộc cho Array {#binding-to-arrays}

Chúng ta có thể ràng buộc `:class` với một mảng để áp dụng một danh sách các class:

<div class="composition-api">

```js
const activeClass = ref('active')
const errorClass = ref('text-danger')
```

</div>

<div class="options-api">

```js
data() {
  return {
    activeClass: 'active',
    errorClass: 'text-danger'
  }
}
```

</div>

```vue-html
<div :class="[activeClass, errorClass]"></div>
```

Nó sẽ render:

```vue-html
<div class="active text-danger"></div>
```

Nếu bạn muốn ràng buộc một class trong danh sách điều kiện, bạn có thể làm điều đó với một biểu thức ba ngôi:

```vue-html
<div :class="[isActive ? activeClass : '', errorClass]"></div>
```

Đoạn mã trên sẽ luôn áp dụng `errorClass`, nhưng `activeClass` chỉ được áp dụng khi `isActive` là truthy.

Tuy nhiên, điều này có thể hơi dài dòng nếu bạn có nhiều class điều kiện. Đó là lý do tại sao bạn có thể sử dụng cú pháp object bên trong cú pháp mảng:

```vue-html
<div :class="[{ active: isActive }, errorClass]"></div>
```

### Với Component {#with-components}

> Phần này giả định bạn đã biết về [Components](/guide/essentials/component-basics). Bạn có thể bỏ qua phần này và đọc sau.

Khi bạn sử dụng thuộc tính `class` trên một component với một phần tử gốc duy nhất, các class đó sẽ được thêm vào phần tử gốc của component và được hợp nhất với bất kỳ class hiện có nào trên nó.

Ví dụ, nếu chúng ta có một component có tên là `MyComponent` với template sau:

```vue-html
<!-- template của component con -->
<p class="foo bar">Hi!</p>
```

Sau đó thêm một vài class khi sử dụng nó:

```vue-html
<!-- khi sử dụng component -->
<MyComponent class="baz boo" />
```

HTML được render sẽ trở thành:

```vue-html
<p class="foo bar baz boo">Hi!</p>
```

Điều tương tự cũng áp dụng cho ràng buộc class:

```vue-html
<MyComponent :class="{ active: isActive }" />
```

Khi `isActive` là truthy, HTML được render sẽ là:

```vue-html
<p class="foo bar active">Hi!</p>
```

Nếu component của bạn có nhiều phần tử gốc, bạn sẽ cần xác định phần tử nào sẽ nhận class này. Bạn có thể làm điều này bằng cách sử dụng thuộc tính `$attrs` của component:

```vue-html
<!-- MyComponent template sử dụng $attrs -->
<p :class="$attrs.class">Hi!</p>
<span>Đây là một component con!</span>
```

```vue-html
<MyComponent class="baz" />
```

Nó sẽ render ra:

```html
<p class="baz">Hi!</p>
<span>This is a child component</span>
```

Bạn có thể tìm hiểu thêm về việc sử dụng `$attrs` trong phần [Inheritance with Attributes](/guide/components/attrs#attribute-inheritance).

## Ràng Inline Styles {#binding-inline-styles}

### Ràng buộc cho Objects {#binding-to-objects-1}

`:style` hỗ trợ ràng buộc cho các giá trị Object trong JavaScript - nó tương ứng với một [HTML element's `style` property](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/style):

<div class="composition-api">

```js
const activeColor = ref('red')
const fontSize = ref(30)
```

</div>

<div class="options-api">

```js
data() {
  return {
    activeColor: 'red',
    fontSize: 30
  }
}
```

</div>

```vue-html
<div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```

Mặc dù các key viết theo camelCase được khuyến khích, `:style` cũng hỗ trợ các key CSS property viết hoa (tương ứng với cách chúng được sử dụng trong CSS thực tế) - ví dụ:

```vue-html
<div :style="{ 'font-size': fontSize + 'px' }"></div>
```

Thông thường thì việc ràng buộc cho một object style trực tiếp là một ý tưởng tốt để template sạch sẽ hơn:

<div class="composition-api">

```js
const styleObject = reactive({
  color: 'red',
  fontSize: '13px'
})
```

</div>

<div class="options-api">

```js
data() {
  return {
    styleObject: {
      color: 'red',
      fontSize: '13px'
    }
  }
}
```

</div>

```vue-html
<div :style="styleObject"></div>
```

Nhắc lại, ràng buộc object style thường được sử dụng kết hợp với các computed property trả về object.

### Ràng buộc cho Array {#binding-to-arrays-1}

Chúng ta có thể ràng buộc `:style` với một mảng bao gồm nhiều object style. Những object này sẽ được kết hợp và áp dụng cùng một element:

```vue-html
<div :style="[baseStyles, overridingStyles]"></div>
```

### Tự động thêm tiền tố {#auto-prefixing}

Khi bạn sử dụng một thuộc tính CSS có yêu cầu [tiền tố của các nhà cung cấp](https://developer.mozilla.org/en-US/docs/Glossary/Vendor_Prefix) trong `:style`, Vue sẽ tự động thêm các tiền tố phù hợp. Vue làm điều này bằng cách kiểm tra tại runtime để xem các thuộc tính style nào được hỗ trợ trong trình duyệt hiện tại. Nếu trình duyệt không hỗ trợ một thuộc tính cụ thể thì các biến thể có tiền tố sẽ được kiểm tra để tìm một biến thể được hỗ trợ.

### Đa giá trị {#multiple-values}

Bạn có thể cung cấp một mảng các giá trị (chứa tiền tố) cho một thuộc tính style, ví dụ:

```vue-html
<div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

Việc này sẽ chỉ render giá trị cuối cùng trong mảng mà trình duyệt hỗ trợ. Trong ví dụ này, nó sẽ render `display: flex` cho trình duyệt hỗ trợ flexbox.
