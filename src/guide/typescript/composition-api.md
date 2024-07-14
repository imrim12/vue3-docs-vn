# TypeScript với Composition API {#typescript-with-composition-api}

> Bài này giả định bạn đã đọc qua [Tổng quan về TypeScript](./overview).

## Ép kiểu cho Component Prop {#typing-component-props}

### Sử dụng `<script setup>` {#using-script-setup}

Khi sử dụng `<script setup>`, macro `defineProps()` hỗ trợ suy luận kiểu của props dựa trên đối số của nó:

```vue
<script setup lang="ts">
const props = defineProps({
  foo: { type: String, required: true },
  bar: Number
})

props.foo // string
props.bar // number | undefined
</script>
```

Việc này được gọi là "khai báo runtime", vì đối số được truyền vào `defineProps()` sẽ được sử dụng như là tùy chọn runtime `props`.

Tuy nhiên, thường thì việc định nghĩa props với các kiểu thuần túy thông qua đối số kiểu generic sẽ dễ dàng hơn:

```vue
<script setup lang="ts">
const props = defineProps<{
  foo: string
  bar?: number
}>()
</script>
```

Việc này được gọi là "khai báo kiểu", trình biên dịch sẽ cố gắng suy luận các tùy chọn runtime tương đương dựa trên đối số kiểu. Trong trường hợp này, ví dụ thứ hai sẽ được biên dịch thành các tùy chọn runtime tương đương với ví dụ thứ nhất.

Bạn có thể sử dụng cả hai loại khai báo kiểu hoặc runtime, nhưng không thể sử dụng cả hai cùng một lúc.

Chúng ta cũng có thể di chuyển các kiểu props vào một interface riêng:

```vue
<script setup lang="ts">
interface Props {
  foo: string
  bar?: number
}

const props = defineProps<Props>()
</script>
```

#### Những giới hạn về cú pháp {#syntax-limitations}

Trong phiên bản 3.2 và thấp hơn, tham số kiểu generic cho `defineProps()` bị giới hạn trong một kiểu chữ hoặc một tham chiếu đến một interface cục bộ.

Giới hạn này đã được giải quyết trong phiên bản 3.3. Phiên bản mới nhất của Vue hỗ trợ tham chiếu đến các kiểu đã được import và một tập hợp hạn chế các kiểu phức tạp trong vị trí tham số kiểu. Tuy nhiên, vì việc chuyển đổi kiểu thành runtime vẫn dựa trên AST, một số kiểu phức tạp yêu cầu phân tích kiểu thực tế, ví dụ như các kiểu điều kiện, không được hỗ trợ. Bạn có thể sử dụng các kiểu điều kiện cho kiểu của một props đơn, nhưng không phải cho toàn bộ đối tượng props.

### Giá trị mặc định cho Props {#props-default-values}

Khi sử dụng khai báo kiểu, chúng ta mất đi khả năng khai báo giá trị mặc định cho props. Điều này có thể được giải quyết bằng macro `withDefaults`:

```ts
export interface Props {
  msg?: string
  labels?: string[]
}

const props = withDefaults(defineProps<Props>(), {
  msg: 'xin chào',
  labels: () => ['một', 'hai']
})
```

Đoạn mã trên sẽ được biên dịch thành các tùy chọn runtime tương đương. Ngoài ra, macro `withDefaults` còn cung cấp kiểm tra kiểu cho các giá trị mặc định, và đảm bảo kiểu của đối tượng `props` trả về sẽ không có các cờ tùy chọn cho các thuộc tính có giá trị mặc định được khai báo.

### Không sử dụng `<script setup>` {#without-script-setup}

Nếu bạn không sử dụng `<script setup>`, thì cần phải sử dụng `defineComponent()` để kích hoạt việc suy luận kiểu cho props. Kiểu của đối tượng props được truyền vào `setup()` được suy luận từ tùy chọn `props`.

```ts
import { defineComponent } from 'vue'

export default defineComponent({
  props: {
    message: String
  },
  setup(props) {
    props.message // <-- kiểu dữ liệu: string
  }
})
```

### Props với kiểu phức tạp {#complex-prop-types}

Với khai báo kiểu, một props có thể sử dụng kiểu phức tạp giống như bất kỳ kiểu nào khác:


```vue
<script setup lang="ts">
interface Book {
  title: string
  author: string
  year: number
}

const props = defineProps<{
  book: Book
}>()
</script>
```

Đối với khai báo runtime, chúng ta có thể sử dụng tiện ích `PropType`:

```ts
import type { PropType } from 'vue'

const props = defineProps({
  book: Object as PropType<Book>
})
```

Tiện ích này hoạt động theo cách tương tự nếu chúng ta đang chỉ định tùy chọn `props` trực tiếp:

```ts
import { defineComponent } from 'vue'
import type { PropType } from 'vue'

export default defineComponent({
  props: {
    book: Object as PropType<Book>
  }
})
```

Tuỳ chọn `props` thường được sử dụng với Options API, vì vậy bạn sẽ tìm thấy các ví dụ chi tiết hơn trong hướng dẫn về [TypeScript với Options API](/guide/typescript/options-api#typing-component-props). Các kỹ thuật được hiển thị trong các ví dụ đó cũng áp dụng cho các khai báo runtime sử dụng `defineProps()`.

## Ép kiểu cho Emit {#typing-component-emits}

Trong `<script setup>`, hàm `emit` cũng có thể được ép kiểu bằng cách sử dụng cả khai báo runtime HOẶC khai báo kiểu:

```vue
<script setup lang="ts">
// runtime
const emit = defineEmits(['change', 'update'])

// type-based
const emit = defineEmits<{
  (e: 'change', id: number): void
  (e: 'update', value: string): void
}>()

// 3.3+: cú pháp thay thế, ngắn gọn hơn
const emit = defineEmits<{
  change: [id: number]
  update: [value: string]
}>()
</script>
```

Kiểu đối số có thể là một trong các kiểu sau:

1. Một kiểu hàm có thể gọi, nhưng được viết dưới dạng một kiểu chữ với [Call Signatures](https://www.typescriptlang.org/docs/handbook/2/functions.html#call-signatures). Nó sẽ được sử dụng như là kiểu của hàm `emit` trả về.
2. Một kiểu chữ trong đó các khóa là tên của các sự kiện, và các giá trị là các kiểu mảng / tuple đại diện cho các tham số được chấp nhận bổ sung cho sự kiện. Ví dụ trên đang sử dụng các tuple được đặt tên để mỗi đối số có thể có một tên rõ ràng.

Như chúng ta có thể thấy, khai báo kiểu cho phép chúng ta kiểm soát ổn định được những ràng buộc kiểu của các sự kiện được phát ra.

Khi không sử dụng `<script setup>`, `defineComponent()` có thể suy luận các sự kiện được phép cho hàm `emit` được phát ra trên ngữ cảnh setup:

```ts
import { defineComponent } from 'vue'

export default defineComponent({
  emits: ['change'],
  setup(props, { emit }) {
    emit('change') // <-- type check / auto-completion
  }
})
```

## Ép kiểu `ref()` {#typing-ref}

Refs được suy luận kiểu từ giá trị ban đầu:

```ts
import { ref } from 'vue'

// kiểu được suy luận: Ref<number>
const year = ref(2020)

// => TS Error: Kiểu 'string' không thể được gán cho kiểu 'number'.
year.value = '2020'
```

Đôi khi chúng ta có thể cần chỉ định các kiểu phức tạp cho giá trị bên trong của một ref. Chúng ta có thể làm điều đó bằng cách sử dụng kiểu `Ref`:

```ts
import { ref } from 'vue'
import type { Ref } from 'vue'

const year: Ref<string | number> = ref('2020')

year.value = 2020 // ok!
```

Hoặc, bằng cách truyền đối số generic khi gọi `ref()` để ghi đè lên suy luận mặc định:

```ts
// resulting type: Ref<string | number>
const year = ref<string | number>('2020')

year.value = 2020 // ok!
```

Nếu bạn chỉ định một đối số generic nhưng bỏ qua giá trị ban đầu, kiểu kết quả sẽ là một kiểu liên hợp bao gồm `undefined`:

```ts
// inferred type: Ref<number | undefined>
const n = ref<number>()
```

## Ép kiểu `reactive()` {#typing-reactive}

`reactive()` cũng suy luận kiểu từ đối số của nó:

```ts
import { reactive } from 'vue'

// inferred type: { title: string }
const book = reactive({ title: 'Vue 3 Guide' })
```

Để chỉ định kiểu `reactive` một cách rõ ràng, chúng ta có thể sử dụng các interface:

```ts
import { reactive } from 'vue'

interface Book {
  title: string
  year?: number
}

const book: Book = reactive({ title: 'Vue 3 Guide' })
```

:::tip
Không nên sử dụng đối số generic của `reactive()` vì kiểu trả về, xử lý việc giải nén ref lồng nhau, khác với kiểu đối số generic.
:::

## Ép kiểu `computed()` {#typing-computed}

`computed()` suy luận kiểu của nó dựa trên giá trị trả về của getter:

```ts
import { ref, computed } from 'vue'

const count = ref(0)

// inferred type: ComputedRef<number>
const double = computed(() => count.value * 2)

// => TS Error: Thuộc tính 'split' không tồn tại với kiểu 'number'
const result = double.value.split('')
```

Bạn cũng có thể chỉ định kiểu trả về một cách rõ ràng thông qua đối số generic:

```ts
const double = computed<number>(() => {
  // lỗi kiểu dữ liệu nếu computed không trả về một số
})
```

## Ép kiểu Event Handlers {#typing-event-handlers}

Khi làm việc với các sự kiện DOM thuần, có thể hữu ích khi ép kiểu đối số chúng ta truyền vào bộ xử lý. Hãy xem xét ví dụ sau:

```vue
<script setup lang="ts">
function handleChange(event) {
  // `event` ngầm định có kiểu `any`
  console.log(event.target.value)
}
</script>

<template>
  <input type="text" @change="handleChange" />
</template>
```

Khi không có chú thích kiểu, đối số `event` sẽ ngầm định có kiểu `any`. Điều này cũng sẽ dẫn đến lỗi TS nếu `"strict": true` hoặc `"noImplicitAny": true` được sử dụng trong `tsconfig.json`. Do đó, nên khai báo rõ ràng đối số của các bộ xử lý sự kiện. Ngoài ra, bạn có thể cần sử dụng các khai báo kiểu khi truy cập các thuộc tính của `event`:

```ts
function handleChange(event: Event) {
  console.log((event.target as HTMLInputElement).value)
}
```

## Ép kiểu Provide / Inject {#typing-provide-inject}

Provide và inject thường được thực hiện trong các component riêng biệt. Để ép kiểu đúng giá trị được inject, Vue cung cấp một interface `InjectionKey`, đây là một kiểu generic mở rộng từ `Symbol`. Nó có thể được sử dụng để đồng bộ kiểu của giá trị được inject giữa provider và consumer:

```ts
import { provide, inject } from 'vue'
import type { InjectionKey } from 'vue'

const key = Symbol() as InjectionKey<string>

provide(key, 'foo')  // khai báo giá trị không phải là string sẽ dẫn đến lỗi

const foo = inject(key) // kiểu dữ liệu của foo: string | undefined
```

Việc đặt khoá inject trong một file riêng biệt được khuyến khích để có thể import vào nhiều component.

Khi sử dụng khoá inject, giá trị được inject sẽ luôn có kiểu `unknown`, và cần được khai báo rõ ràng thông qua đối số generic:

```ts
const foo = inject<string>('foo') // kiểu dữ liệu: string | undefined
```

Hãy chú ý rằng giá trị được inject vẫn có thể là `undefined`, vì không có bất kỳ cung cấp nào đảm bảo rằng giá trị này sẽ được cung cấp tại thời điểm runtime.

Kiểu `undefined` có thể được loại bỏ bằng cách cung cấp một giá trị mặc định:

```ts
const foo = inject<string>('foo', 'bar') // kiểu dữ liệu: string
```

Nếu bạn chắc chắn rằng giá trị sẽ luôn được khai báo, bạn cũng có thể ép kiểu giá trị:

```ts
const foo = inject('foo') as string
```

## Ép kiểu Template Refs {#typing-template-refs}

Template refs nên được tạo ra với một đối số generic rõ ràng và một giá trị ban đầu là `null`:

```vue
<script setup lang="ts">
import { ref, onMounted } from 'vue'

const el = ref<HTMLInputElement | null>(null)

onMounted(() => {
  el.value?.focus()
})
</script>

<template>
  <input ref="el" />
</template>
```

Để lấy được DOM interface đúng, bạn có thể kiểm tra các trang như [MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#technical_summary).

Lưu ý rằng để đảm bảo an toàn kiểu, cần phải sử dụng optional chaining hoặc type guards khi truy cập `el.value`. Điều này bởi vì giá trị ref ban đầu là `null` cho đến khi component được mount, và nó cũng có thể được đặt thành `null` nếu phần tử được tham chiếu bị unmount bởi `v-if`.

## Ép kiểu Component Template Refs {#typing-component-template-refs}

Đôi khi bạn có thể cần chú thích kiểu cho một template ref của một component con để gọi phương thức public của nó. Ví dụ, chúng ta có một component con `MyModal` với một phương thức dùng để mở modal:

```vue
<!-- MyModal.vue -->
<script setup lang="ts">
import { ref } from 'vue'

const isContentShown = ref(false)
const open = () => (isContentShown.value = true)

defineExpose({
  open
})
</script>
```

Để có thể lấy kiểu của instance của `MyModal`, đầu tiên chúng ta cần phải lấy kiểu dữ liệu của nó thông qua `typeof`, sau đó sử dụng tiện ích `InstanceType` được tích hợp sẵn của TypeScript để trích xuất kiểu instance của nó:

```vue{5}
<!-- App.vue -->
<script setup lang="ts">
import MyModal from './MyModal.vue'

const modal = ref<InstanceType<typeof MyModal> | null>(null)

const openModal = () => {
  modal.value?.open()
}
</script>
```

Lưu ý rằng nếu bạn muốn sử dụng kỹ thuật này với các file TypeScript thay vì Vue SFC, bạn cần phải bật [Takeover Mode](./overview#volar-takeover-mode) của Volar.

Trong trường hợp kiểu dữ liệu của component con không có sẵn, hoặc không quan trọng, `ComponentPublicInstance` có thể được sử dụng thay thế. Nó chỉ bao gồm các thuộc tính được chia sẻ bởi tất cả các component, chẳng hạn như `$el`:

```ts
import { ref } from 'vue'
import type { ComponentPublicInstance } from 'vue'

const child = ref<ComponentPublicInstance | null>(null)
```
