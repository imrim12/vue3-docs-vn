# Props {#props}

> Trang này giả định bạn đã đọc qua [Cơ bản về Component](/guide/essentials/component-basics). Hãy đọc trước nếu bạn mới bắt đầu với component.

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-3-reusable-components-with-props" title="Free Vue.js Props Lesson"/>
</div>

## Khai báo Props {#props-declaration}

Vue component yêu cầu khai báo props rõ ràng để Vue biết props bên ngoài được truyền vào component sẽ được xử lý như các thuộc tính truyền tiếp (sẽ được thảo luận trong [thuộc tính truyền tiếp](/guide/components/attrs)).

<div class="composition-api">

Trong các SFC sử dụng `<script setup>`, props có thể được khai báo bằng cách sử dụng macro `defineProps()`:

```vue
<script setup>
const props = defineProps(['foo'])

console.log(props.foo)
</script>
```

Trong các component không sử dụng `<script setup>`, props được khai báo bằng cách sử dụng tùy chọn [`props`](/api/options-state#props):

```js
export default {
  props: ['foo'],
  setup(props) {
    // setup() nhận props như đối số đầu tiên
    console.log(props.foo)
  }
}
```

Chú ý rằng đối số được truyền vào `defineProps()` giống như giá trị được cung cấp cho tùy chọn `props`: cùng một API tùy chọn props được chia sẻ giữa hai kiểu khai báo.

</div>

<div class="options-api">

Prop được khai báo thông qua tuỳ chọn [`props`](/api/options-state#props):

```js
export default {
  props: ['foo'],
  created() {
    // props được khai báo trong `this`
    console.log(this.foo)
  }
}
```

</div>

Bên cạnh việc khai báo props bằng một mảng chuỗi, chúng ta cũng có thể sử dụng cú pháp object:

<div class="options-api">

```js
export default {
  props: {
    title: String,
    likes: Number
  }
}
```

</div>
<div class="composition-api">

```js
// sử dụng <script setup>
defineProps({
  title: String,
  likes: Number
})
```

```js
// không sử dụng <script setup>
export default {
  props: {
    title: String,
    likes: Number
  }
}
```

</div>

Đối với mỗi thuộc tính trong cú pháp khai báo object, key là tên của prop, trong khi value là hàm constructor của kiểu mong đợi.

Việc này không chỉ tài liệu hoá component của bạn, mà còn cảnh báo các nhà phát triển khác sử dụng component của bạn trong trình duyệt nếu họ truyền sai kiểu. Chúng ta sẽ thảo luận thêm chi tiết về [xác thực prop](#prop-validation) ở phía dưới trang này. 

<div class="options-api">

Xem thêm: [Typing Component Props](/guide/components/props#prop-declaration) <sup class="vt-badge vt-badge__api">api</sup>

</div>

<div class="composition-api">

Nếu như bạn đang sử dụng TypeScript với `<script setup>`, bạn cũng có thể khai báo props bằng các type annotation thuần:

```vue
<script setup lang="ts">
defineProps<{
  title?: string
  likes?: number
}>()
</script>
```

Xem thêm: [Typing Component Props](/guide/typescript/composition-api#typing-component-props) <sup class="vt-badge ts" />

</div>

## Truyền thông tin Props {#passing-data-to-props}

### Đặt tên Props {#prop-name-casing}

Chúng ta khai báo tên props sử dụng camelCase vì điều này giúp tránh việc phải sử dụng dấu ngoặc kép khi sử dụng chúng như là key của thuộc tính, và cho phép chúng ta tham chiếu trực tiếp trong biểu thức template vì chúng là các identifier hợp lệ của JavaScript:

<div class="composition-api">

```js
defineProps({
  greetingMessage: String
})
```

</div>
<div class="options-api">

```js
export default {
  props: {
    greetingMessage: String
  }
}
```

</div>

```vue-html
<span>{{ greetingMessage }}</span>
```

Nói một cách kỹ thuật, bạn cũng có thể sử dụng camelCase khi truyền props cho một component con (ngoại trừ trong [in-DOM templates](/guide/essentials/component-basics#in-dom-template-parsing-caveats)). Tuy nhiên, quy ước là sử dụng kebab-case trong tất cả các trường hợp để phù hợp với các thuộc tính HTML:

```vue-html
<MyComponent greeting-message="hello" />
```

Chúng ta sử dụng [PascalCase cho các tag component](/guide/components/registration#component-name-casing) khi có thể vì nó cải thiện khả năng đọc template bằng cách phân biệt các component Vue với các phần tử native. Tuy nhiên, không có nhiều lợi ích thực tế khi sử dụng camelCase khi truyền props, vì vậy chúng ta chọn theo quy ước của mỗi ngôn ngữ.

### Prop tĩnh và động {#static-vs-dynamic-props}

Cho đến giờ, bạn đã thấy props được truyền như các giá trị tĩnh, giống như:

```vue-html
<BlogPost title="My journey with Vue" />
```

Bạn cũng đã thấy props được gán động với `v-bind` hoặc `:` shortcut, như trong:

```vue-html
<!-- Gán giá trị động thông qua biến -->
<BlogPost :title="post.title" />

<!-- Gán giá trị động thông qua một biểu thức phức tạp -->
<BlogPost :title="post.title + ' by ' + post.author.name" />
```

### Truyền các kiểu giá trị khác nhau {#passing-different-value-types}

Trong hai ví dụ trên, chúng ta truyền các giá trị chuỗi, nhưng _bất kỳ_ kiểu giá trị nào cũng có thể được truyền vào một prop.

#### Kiểu số (Number) {#number}

```vue-html
<!-- Mặc dù `42` là giá trị tĩnh, nhưng chúng ta cần sử dụng v-bind để báo với Vue rằng -->
<!-- đây là một biểu thức Javascript chứ không phải là một chuỗi -->
<BlogPost :likes="42" />

<!-- Khai báo giá trị động thông qua biến -->
<BlogPost :likes="post.likes" />
```

#### Kiểu boolean {#boolean}

```vue-html
<!-- Including the prop with no value will imply `true`. -->
<!-- Khai báo prop không kèm với giá trị sẽ mặc định là `true`. -->
<BlogPost is-published />

<!-- Mặc dù `false` là giá trị tĩnh, nhưng chúng ta cần sử dụng v-bind để báo với Vue rằng -->
<!-- đây là một biểu thức Javascript chứ không phải là một chuỗi -->
<BlogPost :is-published="false" />

<!-- Khai báo giá trị động thông qua biến -->
<BlogPost :is-published="post.isPublished" />
```

#### Kiểu mảng (Array) {#array}

```vue-html
<!-- Mặc dù `false` là giá trị tĩnh, nhưng chúng ta cần sử dụng v-bind để báo với Vue rằng -->
<!-- đây là một biểu thức Javascript chứ không phải là một chuỗi -->
<BlogPost :comment-ids="[234, 266, 273]" />

<!-- Khai báo giá trị động thông qua biến -->
<BlogPost :comment-ids="post.commentIds" />
```

#### Kiểu đối tượng (Object) {#object}

```vue-html
<!-- Mặc dù `false` là giá trị tĩnh, nhưng chúng ta cần sử dụng v-bind để báo với Vue rằng -->
<!-- đây là một biểu thức Javascript chứ không phải là một chuỗi -->
<BlogPost
  :author="{
    name: 'Veronica',
    company: 'Veridian Dynamics'
  }"
 />

<!-- Khai báo giá trị động thông qua biến -->
<BlogPost :author="post.author" />
```

### Gán nhiều thuộc tính sử dụng một object {#binding-multiple-properties-using-an-object}

Nếu bạn muốn truyền tất cả các thuộc tính của một object như props, bạn có thể sử dụng [`v-bind` mà không có đối số](/guide/essentials/template-syntax#dynamically-binding-multiple-attributes) (`v-bind` thay vì `:prop-name`). Ví dụ, với một object `post`:

<div class="options-api">

```js
export default {
  data() {
    return {
      post: {
        id: 1,
        title: 'My Journey with Vue'
      }
    }
  }
}
```

</div>
<div class="composition-api">

```js
const post = {
  id: 1,
  title: 'My Journey with Vue'
}
```

</div>

Template sau:

```vue-html
<BlogPost v-bind="post" />
```

Sẽ tương đương với:

```vue-html
<BlogPost :id="post.id" :title="post.title" />
```

## Luồng dữ liệu một chiều {#one-way-data-flow}

Tất cả các props tạo thành một **giá trị một chiều** giữa thuộc tính con và cha: khi thuộc tính cha được cập nhật, nó sẽ lan truyền xuống thuộc tính con, nhưng không thể làm ngược lại. Điều này ngăn các component con vô tình thay đổi trạng thái của component cha, điều này có thể làm cho luồng dữ liệu của ứng dụng khó hiểu hơn.

Thêm vào đó, mọi khi component cha được cập nhật, tất cả các props trong component con sẽ được cập nhật với giá trị mới nhất. Điều này có nghĩa là bạn **không** nên cố gắng thay đổi một prop bên trong một component con. Nếu bạn cố gắng làm như vậy, Vue sẽ cảnh báo bạn trong console:

<div class="composition-api">

```js
const props = defineProps(['foo'])

// ❌ cảnh báo, props là kiểu chỉ-đọc!
props.foo = 'bar'
```

</div>
<div class="options-api">

```js
export default {
  props: ['foo'],
  created() {
    // ❌ cảnh báo, props là kiểu chỉ-đọc!
    this.foo = 'bar'
  }
}
```

</div>

Có hai trường hợp thường xuyên xảy ra mà bạn có thể cảm thấy cần phải thay đổi một prop:

1. **Prop được sử dụng để truyền một giá trị khởi tạo; component con muốn sử dụng nó như một thuộc tính dữ liệu cục bộ sau đó.** Trong trường hợp này, tốt nhất là định nghĩa một thuộc tính dữ liệu cục bộ sử dụng prop như giá trị khởi tạo:

   <div class="composition-api">

   ```js
   const props = defineProps(['initialCounter'])

   // counter chỉ sử dụng props.initialCounter như giá trị khởi tạo;
   // nó không được kết nối với các cập nhật prop trong tương lai.
   const counter = ref(props.initialCounter)
   ```

   </div>
   <div class="options-api">

   ```js
   export default {
     props: ['initialCounter'],
     data() {
       return {
         // counter chỉ sử dụng this.initialCounter như giá trị khởi tạo;
         // nó không được kết nối với các cập nhật prop trong tương lai.
         counter: this.initialCounter
       }
     }
   }
   ```

   </div>

2. **Prop được truyền vào như một biến hoặc một đối tượng, sau đó component con muốn "biến đổi" prop này thành một dữ liệu cục bộ.** Trong trường hợp này, tốt nhất là định nghĩa một computed property sử dụng prop như một giá trị đầu vào:

   <div class="composition-api">

   ```js
   const props = defineProps(['user'])

   // computed property sử dụng props.user như một giá trị đầu vào
   const userId = computed(() => props.user.id)
   ```

   </div>
   <div class="options-api">

   ```js
   export default {
     props: ['user'],
     computed: {
       // computed property sử dụng this.user như một giá trị đầu vào
       userId() {
         return this.user.id
       }
     }
   }
   ```

   </div>

### Biến đổi prop Object và Array {#transforming-objects-and-arrays-as-props}

Khi object hoặc array được truyền vào như một prop, trong khi component con không thể thay đổi prop, nó vẫn **sẽ** có thể thay đổi các thuộc tính hoặc phần tử của object hoặc array đó. Điều này là do trong JavaScript, object và array được truyền bởi tham chiếu, và việc ngăn chặn thay đổi sẽ khiến Vue trở tốn kém hơn rất nhiều.

Bất lợi chính của việc này đó là nó sẽ cho phép các component con tác động lên trạng thái của component cha mà không làm cho luồng dữ liệu của chúng trở nên khó hiểu. Theo như quy tắc, bạn nên tránh các biến đổi không mong muốn như vậy trừ khi component cha và con được thiết kế để làm việc với nhau một cách chặt chẽ. Trong hầu hết các trường hợp, component con nên [emit một sự kiện](/guide/components/events) để cho phép component cha thực hiện biến đổi.

## Xác thực prop {#prop-validation}

Component có thể chỉ định các yêu cầu cho props của chúng, chẳng hạn như các kiểu bạn đã thấy. Nếu một yêu cầu không được đáp ứng, Vue sẽ cảnh báo bạn trong console của trình duyệt. Điều này đặc biệt hữu ích khi phát triển một component được dự định sẽ được sử dụng bởi người khác.

Để chỉ định xác thực, bạn có thể cung cấp một object với các yêu cầu thay vì một mảng chuỗi cho  <span class="composition-api">`defineProps()` macro</span><span class="options-api">`props` option</span>. Ví dụ:

<div class="composition-api">

```js
defineProps({
  // Kiểm tra kiểu cơ bản
  // (giá trị `null` và `undefined` sẽ cho phép bất kỳ kiểu nào)
  propA: Number,
  // Nhiều kiểu khả dụng
  propB: [String, Number],
  // Bắt buộc chuỗi
  propC: {
    type: String,
    required: true
  },
  // Kiểu số với giá trị mặc định
  propD: {
    type: Number,
    default: 100
  },
  // Kiểu đối tượng với giá trị mặc định
  propE: {
    type: Object,
    // Kiểu đối tượng hoặc mảng mặc định phải được trả về từ
    // một hàm factory. Hàm nhận các props gốc
    // nhận được bởi component như là đối số.
    default(rawProps) {
      return { message: 'hello' }
    }
  },
  // Hàm xác thực tùy chỉnh
  propF: {
    validator(value) {
      // Giá trị phải khớp với một trong các chuỗi này
      return ['success', 'warning', 'danger'].includes(value)
    }
  },
  // Hàm với giá trị mặc định
  propG: {
    type: Function,
    // Không giống như mặc định đối tượng hoặc mảng, đây không phải là một hàm factory
    // đây là một hàm để phục vụ như một giá trị mặc định
    default() {
      return 'Default function'
    }
  }
})
```

:::tip
Code bên trong đối số của `defineProps()` **không thể truy cập các biến khác được khai báo trong `<script setup>`**, vì toàn bộ biểu thức được di chuyển đến một phạm vi hàm bên ngoài khi được biên dịch.
:::

</div>
<div class="options-api">

```js
export default {
  props: {
    // Kiểm tra kiểu cơ bản
    // (giá trị `null` và `undefined` sẽ cho phép bất kỳ kiểu nào)
    propA: Number,
    // Nhiều kiểu khả dụng
    propB: [String, Number],
    // Bắt buộc chuỗi
    propC: {
      type: String,
      required: true
    },
    // Kiểu số với giá trị mặc định
    propD: {
      type: Number,
      default: 100
    },
    // Kiểu đối tượng với giá trị mặc định
    propE: {
      type: Object,
      // Kiểu đối tượng hoặc mảng mặc định phải được trả về từ
      // một hàm factory. Hàm nhận các props gốc
      // nhận được bởi component như là đối số.
      default(rawProps) {
        return { message: 'hello' }
      }
    },
    // Hàm xác thực tùy chỉnh
    propF: {
      validator(value) {
        // Giá trị phải khớp với một trong các chuỗi này
        return ['success', 'warning', 'danger'].includes(value)
      }
    },
    // Hàm với giá trị mặc định
    propG: {
      type: Function,
      // Không giống như mặc định đối tượng hoặc mảng, đây không phải là một hàm factory
      // đây là một hàm để phục vụ như một giá trị mặc định
      default() {
        return 'Default function'
      }
    }
  }
}
```

</div>

Thông tin thêm:

- Tất cả các props đều là tùy chọn theo mặc định, trừ khi `required: true` được chỉ định.

- Một prop tuỳ chọn không phải `Boolean` mà không được truyền vào sẽ có giá trị `undefined`.

- Prop tuỳ chọn `Boolean` không được truyền vào sẽ có giá trị `false`. Bạn có thể thay đổi điều này bằng cách đặt `default` cho nó - ví dụ: `default: undefined` để hoạt động như một prop không phải `Boolean`.

- Nếu một giá trị `default` được chỉ định, nó sẽ sử dụng giá trị đó nếu prop không được truyền vào (ví dụ: `undefined`) hoặc nếu prop được truyền vào với giá trị `undefined`.

Khi xác thực prop thất bại, Vue sẽ tạo ra một cảnh báo trong console của trình duyệt (nếu sử dụng bản phát triển).

<div class="composition-api">

Nếu sử dụng [Type-based props declarations](/api/sfc-script-setup#type-only-props-emit-declarations) <sup class="vt-badge ts" />, Vue sẽ cố gắng biên dịch các chú thích kiểu thành các khai báo prop runtime tương đương. Ví dụ, `defineProps<{ msg: string }>` sẽ được biên dịch thành `{ msg: { type: String, required: true }}`.

</div>
<div class="options-api">

::: tip Note
Chú ý rằng các props được xác thực **trước** khi một instance component được tạo ra, vì vậy các thuộc tính instance (ví dụ: `data`, `computed`, v.v.) sẽ không có sẵn trong các hàm `default` hoặc `validator`.
:::

</div>

### Kiểm tra kiểu trong Runtime {#type-checks}

`type` có thể là một trong các constructor native sau đây:

- `String`
- `Number`
- `Boolean`
- `Array`
- `Object`
- `Date`
- `Function`
- `Symbol`

Thêm vào đó, `type` cũng có thể là một class tuỳ chỉnh hoặc một hàm constructor và sẽ được sử dụng với `instanceof` để kiểm tra xem prop có hợp lệ hay không. Ví dụ:

```js
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName
    this.lastName = lastName
  }
}
```

Bạn có thể sử dụng `Person` như là một kiểu prop:

<div class="composition-api">

```js
defineProps({
  author: Person
})
```

</div>
<div class="options-api">

```js
export default {
  props: {
    author: Person
  }
}
```

</div>

Vue sẽ sử dụng `instanceof Person` để kiểm tra xem giá trị của prop `author` có phải là một instance của class `Person` hay không.

## Ép kiểu Boolean {#boolean-casting}

Prop với kiểu `Boolean` có các quy tắc ép kiểu đặc biệt để mô phỏng hành vi của các thuộc tính boolean native. Cho một `<MyComponent>` với khai báo sau:

<div class="composition-api">

```js
defineProps({
  disabled: Boolean
})
```

</div>
<div class="options-api">

```js
export default {
  props: {
    disabled: Boolean
  }
}
```

</div>

Component có thể được sử dụng như sau:

```vue-html
<!-- equivalent of passing :disabled="true" -->
<MyComponent disabled />

<!-- equivalent of passing :disabled="false" -->
<MyComponent />
```

Khi một prop được khai báo với nhiều kiểu, các quy tắc ép kiểu cho `Boolean` sẽ được áp dụng. Tuy nhiên, có một trường hợp đặc biệt khi cả `String` và `Boolean` đều được cho phép - quy tắc ép kiểu của `Boolean` chỉ được áp dụng nếu `Boolean` xuất hiện trước `String`:

<div class="composition-api">

```js
// disabled sẽ được ép kiểu thành true
defineProps({
  disabled: [Boolean, Number]
})
  
// disabled sẽ được ép kiểu thành true
defineProps({
  disabled: [Boolean, String]
})
  
// disabled sẽ được ép kiểu thành true
defineProps({
  disabled: [Number, Boolean]
})
  
// disabled sẽ được ép kiểu thành chuỗi và có giá trị rỗng (disabled="")
defineProps({
  disabled: [String, Boolean]
})
```

</div>
<div class="options-api">

```js
// disabled sẽ được ép kiểu thành true
export default {
  props: {
    disabled: [Boolean, Number]
  }
}
  
// disabled sẽ được ép kiểu thành true
export default {
  props: {
    disabled: [Boolean, String]
  }
}
  
// disabled sẽ được ép kiểu thành true
export default {
  props: {
    disabled: [Number, Boolean]
  }
}
  
// disabled sẽ được ép kiểu thành chuỗi và có giá trị rỗng (disabled="")
export default {
  props: {
    disabled: [String, Boolean]
  }
}
```

</div>
