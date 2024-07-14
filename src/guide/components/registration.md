# Đăng ký Component {#component-registration}

> Trang này giả định bạn đã đọc qua [Cơ bản về Component](/guide/essentials/component-basics). Hãy đọc trước nếu bạn mới bắt đầu với component.

<VueSchoolLink href="https://vueschool.io/lessons/vue-3-global-vs-local-vue-components" title="Free Vue.js Component Registration Lesson"/>

Một component Vue cần được "đăng ký" để Vue biết nơi tìm thấy cài đặt của nó khi nó được gặp trong một template. Có hai cách để đăng ký component: toàn cục và cục bộ.

## Đăng ký component toàn cục {#global-registration}

Chúng ta có thể làm cho component khả dụng trong toàn bộ ứng dụng Vue hiện tại bằng cách sử dụng phương thức `app.component()`:

```js
import { createApp } from 'vue'

const app = createApp({})

app.component(
  // tên của component được đăng ký
  'MyComponent',
  // cấu hình component
  {
    /* ... */
  }
)
```

Nếu sử dụng SFC, bạn sẽ đăng ký các file `.vue` được import:

```js
import MyComponent from './App.vue'

app.component('MyComponent', MyComponent)
```

Phương thức `app.component()` có thể được gọi nhiều lần:

```js
app
  .component('ComponentA', ComponentA)
  .component('ComponentB', ComponentB)
  .component('ComponentC', ComponentC)
```

Component đăng ký toàn cục có thể được sử dụng trong template của bất kỳ component nào trong ứng dụng này:

```vue-html
<!-- những component này sẽ hoạt động trong bất kỳ component nào trong app -->
<ComponentA/>
<ComponentB/>
<ComponentC/>
```

Điều này cũng áp dụng cho tất cả các subcomponent, có nghĩa là tất cả ba component này cũng sẽ có sẵn _bên trong nhau_.

## Đăng ký component cục bộ {#local-registration}

Mặc dù đăng ký toàn cục là tiện lợi, nó có một số nhược điểm:

1. Việc đăng ký toàn cục ngăn hệ thống build loại bỏ các component không được sử dụng (còn được gọi là "tree-shaking"). Nếu bạn đăng ký một component toàn cầu nhưng cuối cùng không sử dụng nó ở bất kỳ đâu trong ứng dụng của bạn, nó vẫn sẽ được thêm vào trong bundle cuối cùng.

2. Việc đăng ký toàn cục làm cho mối quan hệ phụ thuộc ít rõ ràng hơn trong các ứng dụng lớn. Nó làm cho việc tìm vị trí triển khai của một component con từ một component cha sử dụng nó trở nên khó khăn hơn. Điều này có thể ảnh hưởng đến khả năng bảo trì lâu dài tương tự như việc sử dụng quá nhiều biến toàn cục.

Đăng ký cục bộ giới hạn sự khả dụng của các component đã đăng ký cho component hiện tại. Nó làm cho mối quan hệ phụ thuộc rõ ràng hơn và thân thiện với tree-shaking.

<div class="composition-api">

Khi sử dụng SFC với `<script setup>`, component được import có thể được sử dụng một cách cục bộ mà không cần đăng ký:

```vue
<script setup>
import ComponentA from './ComponentA.vue'
</script>

<template>
  <ComponentA />
</template>
```

Nếu không sử dụng `<script setup>`, bạn sẽ cần sử dụng tùy chọn `components`:

```js
import ComponentA from './ComponentA.js'

export default {
  components: {
    ComponentA
  },
  setup() {
    // ...
  }
}
```

</div>
<div class="options-api">

Việc đăng ký cục bộ được thực hiện bằng cách cung cấp một `components` object trong các tùy chọn của component:

```vue
<script>
import ComponentA from './ComponentA.vue'

export default {
  components: {
    ComponentA
  }
}
</script>

<template>
  <ComponentA />
</template>
```

</div>

Đối với mỗi thuộc tính trong object `components`, key sẽ là tên đã đăng ký của component, trong khi value sẽ chứa cài đặt của component. Ví dụ trên đang sử dụng ES2015 property shorthand và tương đương với:

```js
export default {
  components: {
    ComponentA: ComponentA
  }
  // ...
}
```

Lưu ý rằng **các component cục bộ _không_ khả dụng cho các component con của chúng**. Trong ví dụ trên, `ComponentA` sẽ chỉ có sẵn cho component hiện tại, không phải cho bất kỳ component con hoặc component cháu nào.

## Cách đặt tên Component {#component-name-casing}

Trong suốt hướng dẫn này, chúng tôi đang sử dụng cách đặt tên PascalCase khi đăng ký component. Điều này bởi vì:

1. PascalCase là các JavaScript identifier hợp lệ. Điều này làm cho việc import và đăng ký component trong JavaScript dễ dàng hơn. Nó cũng giúp cho IDE sử dụng auto-completion.

2. `<PascalCase />` sẽ khiến cho nó dễ dàng hơn để phân biệt component Vue với các phần tử HTML native trong template. Nó cũng phân biệt component Vue với các phần tử tùy chỉnh (web components).

Việc này cũng có nghĩa là chúng tôi khuyến khích sử dụng PascalCase khi đặt tên component trong template. Tuy nhiên, như đã thảo luận trong [Những lưu ý khi parsing template trong DOM](/guide/essentials/component-basics#in-dom-template-parsing-caveats), các thẻ PascalCase không thể sử dụng trong DOM template.

May mắn là, Vue có hỗ trợ xử lý các thẻ kebab-case thành component đã đăng ký bằng PascalCase. Điều này có nghĩa là một component được đăng ký là `MyComponent` có thể được tham chiếu trong template thông qua cả `<MyComponent>` và `<my-component>`. Điều này cho phép chúng tôi sử dụng cùng một mã đăng ký component JavaScript bất kể nguồn template.
