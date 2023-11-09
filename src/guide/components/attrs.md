---
outline: deep
---

# Thuộc tính truyền tiếp {#fallthrough-attributes}

> Trang này giả định bạn đã đọc qua [Cơ bản về Component](/guide/essentials/component-basics). Hãy đọc trước nếu bạn mới bắt đầu với component.

## Tính kế thừa thuộc tính {#attribute-inheritance}

Một "thuộc tính truyền tiếp" là một thuộc tính hoặc `v-on` event listener được truyền vào một component, nhưng không được khai báo rõ ràng trong [props](./props) hoặc [emits](./events#declaring-emitted-events) của component được nhận. Ví dụ phổ biến của điều này bao gồm các thuộc tính `class`, `style` và `id`.

Khi một component hiển thị một phần tử gốc duy nhất, thuộc tính truyền tiếp sẽ được tự động thêm vào các thuộc tính của phần tử gốc. Ví dụ, cho một component `<MyButton>` với template như sau:

```vue-html
<!-- template of <MyButton> -->
<button>nhấn vào tôi</button>
```

Và một component cha sử dụng component này với:

```vue-html
<MyButton class="large" />
```

DOM cuối cùng sẽ là:

```html
<button class="large">nhấn vào tôi</button>
```

Tại đây, `<MyButton>` không khai báo `class` như một prop. Do đó, `class` được coi là một thuộc tính truyền tiếp và tự động được thêm vào phần tử gốc của `<MyButton>`.

### Kết hợp `class` và `style` {#class-and-style-merging}

Nếu phần tử gốc của component con đã có các thuộc tính `class` hoặc `style` hiện có, nó sẽ được kết hợp với các giá trị `class` và `style` được kế thừa từ component cha. Giả sử chúng ta thay đổi template của `<MyButton>` trong ví dụ trước thành:

```vue-html
<!-- template of <MyButton> -->
<button class="btn">nhấn vào tôi</button>
```

Lúc này DOM cuối cùng sẽ trở thành:

```html
<button class="btn large">nhấn vào tôi</button>
```

### Tính kế thừa `v-on` {#v-on-inheritance}

Quy tắc tương tự cũng áp dụng cho `v-on` event listener:

```vue-html
<MyButton @click="onClick" />
```

Việc lắng nghe `click` sẽ được thêm vào phần tử gốc của `<MyButton>`, tức là phần tử `<button>` native. Khi phần tử `<button>` native được nhấn, nó sẽ kích hoạt phương thức `onClick` của component cha. Nếu phần tử `<button>` native đã có một `click` listener được ràng buộc với `v-on`, thì cả hai listener sẽ được kích hoạt.

### Tính kế thừa component lồng nhau {#nested-component-inheritance}

Nếu một component hiển thị một component khác làm phần tử gốc, ví dụ, chúng ta đã tái cấu trúc `<MyButton>` để hiển thị một `<BaseButton>` làm phần tử gốc:

```vue-html
<!-- <MyButton/> template dùng để render một component khác -->
<BaseButton />
```

Lúc này các thuộc tính truyền tiếp nhận được bởi `<MyButton>` sẽ được tự động chuyển tiếp cho `<BaseButton>`. Nghĩa là các thuộc tính truyền tiếp như `class` và `v-on` listener sẽ được áp dụng cho phần tử gốc của `<BaseButton>`.

Lưu ý rằng:

1. Các thuộc tính truyền tiếp không bao gồm bất kỳ thuộc tính được khai báo là props, hoặc `v-on` listener của các event được khai báo bởi `<MyButton>` - nói cách khác, các props và listener được khai báo đã được "tiêu thụ" bởi `<MyButton>`.

2. Các thuộc tính truyền tiếp có thể được chấp nhận như props bởi `<BaseButton>`, nếu được khai báo bởi nó.

## Ngăn chặn kế thừa thuộc tính {#preventing-attribute-inheritance}

Nếu như bạn **không** muốn một component tự động kế thừa các thuộc tính, bạn có thể đặt `inheritAttrs: false` trong các tùy chọn của component.

<div class="composition-api">

 Bắt đầu từ Vue 3.3, bạn cũng có thể sử dụng [`defineOptions`](/api/sfc-script-setup#defineoptions) trực tiếp trong `<script setup>`:

```vue
<script setup>
defineOptions({
  inheritAttrs: false
})
// ...thiết lập các logic khác
</script>
```

</div>

Trường hợp vô hiệu hóa kế thừa thuộc tính phổ biến là khi các thuộc tính cần được áp dụng cho các phần tử khác ngoài phần tử gốc. Bằng cách đặt tùy chọn `inheritAttrs` thành `false`, bạn có thể kiểm soát đầy đủ các phần tử nào sẽ được áp dụng các thuộc tính truyền tiếp.

Những thuộc tính truyền tiếp này có thể được truy cập trực tiếp trong các biểu thức template như `$attrs`:

```vue-html
<span>Các thuộc tính truyền tiếp: {{ $attrs }}</span>
```

Object `$attrs` bao gồm tất cả các thuộc tính không được khai báo bởi các tùy chọn `props` hoặc `emits` của component (ví dụ: `class`, `style`, `v-on` listeners, v.v.).

Một vài lưu ý:

- Không giống như prop, các thuộc tính truyền tiếp giữ nguyên kiểu viết của chúng trong JavaScript, vì vậy một thuộc tính như `foo-bar` cần được truy cập như `$attrs['foo-bar']`.

- Một lắng nghe `v-on` như `@click` sẽ được có thể được truy cập trong object như một function dưới dạng `$attrs.onClick`.

Sử dụng ví dụ về component `<MyButton>` từ [phần trước](#attribute-inheritance), chúng ta có thể thấy rằng `inheritAttrs: false` sẽ ngăn chặn các thuộc tính truyền tiếp được tự động áp dụng cho phần tử gốc:

```vue-html
<div class="btn-wrapper">
  <button class="btn">nhấn vào tôi</button>
</div>
```

Chúng ta muốn tất cả các thuộc tính truyền tiếp như `class` và `v-on` listeners được áp dụng cho phần tử `<button>` bên trong, chứ không phải phần tử `<div>` bên ngoài. Chúng ta có thể đạt được điều này với `inheritAttrs: false` và `v-bind="$attrs"`:

```vue-html{2}
<div class="btn-wrapper">
  <button class="btn" v-bind="$attrs">nhấn vào tôi</button>
</div>
```

Hãy nhớ rằng [`v-bind` mà không có đối số](/guide/essentials/template-syntax#dynamically-binding-multiple-attributes) sẽ áp dụng tất cả các thuộc tính của một object như các thuộc tính của phần tử mục tiêu.

## Tính kế thừa thuộc tính trên nhiều phần tử gốc {#attribute-inheritance-on-multiple-root-nodes}

Không giống như các component chỉ có một phần tử gốc, các component có nhiều phần tử gốc sẽ không có hành vi kế thừa thuộc tính truyền tiếp tự động. Nếu `$attrs` không được ràng buộc rõ ràng, một cảnh báo sẽ được phát ra.

```vue-html
<CustomLayout id="custom-layout" @click="changeValue" />
```

Nếu `<CustomLayout>` có template bao gồm nhiều phần tử gốc như dưới đây, sẽ có một cảnh báo vì Vue không thể chắc chắn nơi nào để áp dụng các thuộc tính truyền tiếp:

```vue-html
<header>...</header>
<main>...</main>
<footer>...</footer>
```

Cảnh báo này có thể được giải quyết bằng cách ràng buộc `$attrs` với một phần tử gốc duy nhất:

```vue-html{2}
<header>...</header>
<main v-bind="$attrs">...</main>
<footer>...</footer>
```

## Truy cập thuộc tính truyền tiếp trong JavaScript {#accessing-fallthrough-attributes-in-javascript}

<div class="composition-api">

Nếu cần thiết, bạn có thể truy cập các thuộc tính truyền tiếp của một component trong `<script setup>` bằng cách sử dụng API `useAttrs()`:

```vue
<script setup>
import { useAttrs } from 'vue'

const attrs = useAttrs()
</script>
```

Nếu không sử dụng `<script setup>`, `attrs` sẽ được truy cập như một thuộc tính của `setup()` context:

```js
export default {
  setup(props, ctx) {
    // thuộc tính truyền tiếp được truy cập thông qua ctx.attrs
    console.log(ctx.attrs)
  }
}
```

Lưu ý rằng mặc dù object `attrs` ở đây luôn phản ánh các thuộc tính truyền tiếp mới nhất, nó không phải là reactive (vì lý do hiệu năng). Bạn không thể sử dụng watchers để quan sát các thay đổi của nó. Nếu bạn cần tính phản ứng, hãy sử dụng prop. Ngoài ra, bạn có thể sử dụng `onUpdated()` để thực hiện các hiệu ứng phụ với `attrs` mới nhất trên mỗi lần cập nhật.

</div>

<div class="options-api">

Nếu cần thiết, bạn có thể truy cập các thuộc tính truyền tiếp của một component thông qua `$attrs` instance property:

```js
export default {
  created() {
    console.log(this.$attrs)
  }
}
```

</div>
