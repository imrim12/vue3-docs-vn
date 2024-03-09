# Composables {#composables}

<script setup>
import { useMouse } from './mouse'
const { x, y } = useMouse()
</script>

:::tip
Phần này giả định bạn đã có kiến thức cơ bản về Composition API. Nếu bạn chỉ học Vue với Options API, bạn có thể đặt API Preference thành Composition API (sử dụng toggle ở đầu của thanh bên trái) và đọc lại các chương [Reactivity Fundamentals](/guide/essentials/reactivity-fundamentals) và [Lifecycle Hooks](/guide/essentials/lifecycle).
:::

## "Composable" là cái gì? {#what-is-a-composable}

Trong ngữ cảnh của ứng dụng Vue, một "composable" là một hàm sử dụng Composition API của Vue để đóng gói và tái sử dụng **logic có trạng thái**.

Khi xây dựng các ứng dụng frontend, chúng ta thường cần tái sử dụng logic cho các nhiệm vụ phổ biến. Ví dụ, chúng ta có thể cần định dạng ngày tháng ở nhiều nơi, vì vậy chúng ta trích xuất một hàm tái sử dụng cho điều đó. Hàm định dạng này bao gồm **logic không có trạng thái**: nó nhận một số đầu vào và ngay lập tức trả về một đầu ra theo mong muốn. Có nhiều thư viện để tái sử dụng logic không có trạng thái - ví dụ như [lodash](https://lodash.com/) và [date-fns](https://date-fns.org/), mà bạn có thể đã nghe qua.

Ngược lại, logic có trạng thái liên quan đến việc quản lý trạng thái thay đổi theo thời gian. Một ví dụ đơn giản có thể là theo dõi vị trí hiện tại của chuột trên trang. Trong các kịch bản thực tế, nó cũng có thể là logic phức tạp hơn như cử chỉ chạm hoặc trạng thái kết nối đến cơ sở dữ liệu.

## Ví dụ về Truy dấu con trỏ chuột {#mouse-tracker-example}

Nếu chúng ta triển khai chức năng truy dấu chuột bằng Composition API trực tiếp bên trong một component, nó sẽ trông như thế này:

```vue
<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const x = ref(0)
const y = ref(0)

function update(event) {
  x.value = event.pageX
  y.value = event.pageY
}

onMounted(() => window.addEventListener('mousemove', update))
onUnmounted(() => window.removeEventListener('mousemove', update))
</script>

<template>Vị trí hiện tại của con trỏ: {{ x }}, {{ y }}</template>
```

Nhưng nếu chúng ta muốn tái sử dụng cùng một logic trong nhiều component? Chúng ta có thể trích xuất logic vào một tệp bên ngoài, như một hàm có thể tái sử dụng:

```js
// mouse.js
import { ref, onMounted, onUnmounted } from 'vue'

// theo quy chuẩn, tên hàm composable bắt đầu bằng "use"
export function useMouse() {
  // state được đóng gói và quản lý bởi composable
  const x = ref(0)
  const y = ref(0)

  // một composable có thể cập nhật các state mà nó quản lý theo thời gian.
  function update(event) {
    x.value = event.pageX
    y.value = event.pageY
  }

  // một composable cũng có thể kết nối với vòng đời của component chủ 
  // để thiết lập và hủy các hiệu ứng phụ.
  onMounted(() => window.addEventListener('mousemove', update))
  onUnmounted(() => window.removeEventListener('mousemove', update))

  // trả về các state được quản lý để sử dụng trong component
  return { x, y }
}
```

Và đây là cách nó có thể được sử dụng trong các component:

```vue
<script setup>
import { useMouse } from './mouse.js'

const { x, y } = useMouse()
</script>

<template>Vị trí hiện tại của con trỏ: {{ x }}, {{ y }}</template>
```

<div class="demo">
  Vị trí hiện tại của con trỏ: {{ x }}, {{ y }}
</div>

[Thử trong Playground](https://play.vuejs.org/#eNqNkj1rwzAQhv/KocUOGKVzSAIdurVjoQUvJj4XlfgkJNmxMfrvPcmJkkKHLrbu69H7SlrEszFyHFDsxN6drDIeHPrBHGtSvdHWwwKDwzfNHwjQWd1DIbd9jOW3K2qq6aTJxb6pgpl7Dnmg3NS0365YBnLgsTfnxiNHACvUaKe80gTKQeN3sDAIQqjignEhIvKYqMRta1acFVrsKtDEQPLYxuU7cV8Msmg2mdTilIa6gU5p27tYWKKq1c3ENphaPrGFW25+yMXsHWFaFlfiiOSvFIBJjs15QJ5JeWmaL/xYS/Mfpc9YYrPxl52ULOpwhIuiVl9k07Yvsf9VOY+EtizSWfR6xKK6itgkvQ/+fyNs6v4XJXIsPwVL+WprCiL8AEUxw5s=)

Như chúng ta có thể thấy, phần logic cốt lõi vẫn được giữ nguyên - tất cả những gì chúng ta cần là di chuyển nó vào một hàm bên ngoài và trả về state mà nên được tiếp cận. Giống như trong một component, bạn có thể sử dụng toàn bộ loạt các [Composition API functions](/api/#composition-api) trong composables. Cùng một hàm `useMouse()` có thể được sử dụng trong bất kỳ component nào.

Phần tuyệt vời hơn về composable đó chính là bạn cũng có thể lồng chúng vào nhau: một hàm composable có thể gọi một hoặc nhiều hàm composable khác. Điều này cho phép chúng ta kết hợp logic phức tạp bằng cách sử dụng các đơn vị nhỏ, cô lập, tương tự như cách chúng ta kết hợp toàn bộ ứng dụng bằng các component. Trong thực tế, đây là lý do tại sao chúng tôi quyết định gọi tập hợp các API là Composition API.

Ví dụ, chúng ta có thể trích xuất logic của việc thực hiện và xóa một sự kiện DOM thành một composable riêng:

```js
// event.js
import { onMounted, onUnmounted } from 'vue'

export function useEventListener(target, event, callback) {
  // nếu bạn muốn, bạn cũng có thể làm cho nó 
  // hỗ trợ chuỗi chọn như mục tiêu
  onMounted(() => target.addEventListener(event, callback))
  onUnmounted(() => target.removeEventListener(event, callback))
}
```

Và bây giờ composable `useMouse()` của chúng ta có thể được đơn giản hóa thành:

```js{3,9-12}
// mouse.js
import { ref } from 'vue'
import { useEventListener } from './event'

export function useMouse() {
  const x = ref(0)
  const y = ref(0)

  useEventListener(window, 'mousemove', (event) => {
    x.value = event.pageX
    y.value = event.pageY
  })

  return { x, y }
}
```

:::tip
Mỗi component instance gọi `useMouse()` sẽ tạo ra bản sao riêng của state `x` và `y` nên chúng sẽ không can thiệp vào nhau. Nếu bạn muốn quản lý state chia sẻ giữa các component, hãy đọc chương [State Management](/guide/scaling-up/state-management).
:::

## Ví dụ về State bất đồng bộ State {#async-state-example}

`useMouse()` composable không nhận bất kỳ đối số nào, vì vậy hãy xem xét một ví dụ khác mà sử dụng một đối số. Khi thực hiện việc lấy dữ liệu bất đồng bộ, chúng ta thường cần xử lý các trạng thái khác nhau: đang tải, thành công và lỗi:

```vue
<script setup>
import { ref } from 'vue'

const data = ref(null)
const error = ref(null)

fetch('...')
  .then((res) => res.json())
  .then((json) => (data.value = json))
  .catch((err) => (error.value = err))
</script>

<template>
  <div v-if="error">Ối! Gặp phải lỗi rồi: {{ error.message }}</div>
  <div v-else-if="data">
    Data đã tải xong:
    <pre>{{ data }}</pre>
  </div>
  <div v-else>Đang tải...</div>
</template>
```

Việc lặp lại mẫu này trong mỗi component cần lấy dữ liệu sẽ trở nên khá kỳ cục. Hãy trích xuất nó thành một composable:

```js
// fetch.js
import { ref } from 'vue'

export function useFetch(url) {
  const data = ref(null)
  const error = ref(null)

  fetch(url)
    .then((res) => res.json())
    .then((json) => (data.value = json))
    .catch((err) => (error.value = err))

  return { data, error }
}
```

Bây giờ trong component, chúng ta chỉ cần:

```vue
<script setup>
import { useFetch } from './fetch.js'

const { data, error } = useFetch('...')
</script>
```

### Chấp nhận các Reactive State {#accepting-reactive-state}

`useFetch()` chỉ nhận một chuỗi URL tĩnh làm đầu vào - vì vậy nó thực hiện việc lấy dữ liệu chỉ một lần và sau đó là xong. Nhưng nếu chúng ta muốn nó lấy lại mỗi khi URL thay đổi? Để làm được điều này, chúng ta cần truyền reactive state vào hàm composable, và để composable tạo ra các watcher thực hiện hành động sử dụng state đã truyền vào.

Ví dụ, `useFetch()` nên có thể chấp nhận một ref:

```js
const url = ref('/initial-url')

const { data, error } = useFetch(url)

// việc này nên kích hoạt lại fetch
url.value = '/new-url'
```

Hoặc, chấp nhận một hàm getter:

```js
// fetch lại khi props.id thay đổi
const { data, error } = useFetch(() => `/posts/${props.id}`)
```

Chúng ta có thể tái cấu trúc triển khai hiện tại của chúng ta với các API [`watchEffect()`](/api/reactivity-core.html#watcheffect) và [`toValue()`](/api/reactivity-utilities.html#tovalue):

```js{8,13}
// fetch.js
import { ref, watchEffect, toValue } from 'vue'

export function useFetch(url) {
  const data = ref(null)
  const error = ref(null)

	const fetchData = (dt) => {
			fetch(toValue(url))
      .then((res) => res.json())
      .then((json) => (data.value = json))
      .catch((err) => (error.value = err))
	}

  watchEffect(() => {
    // thiết lập lại state trước khi fetching..
    fetchData(url)
  })

  return { data, error }
}
```

`toValue()` là một API được thêm vào từ phiên bản 3.3. Nó được thiết kế để chuẩn hóa refs hoặc getters thành giá trị. Nếu đối số là một ref, nó sẽ trả về giá trị của ref; nếu đối số là một hàm, nó sẽ gọi hàm và trả về giá trị trả về của nó. Nếu không, nó sẽ trả về đối số như một giá trị. Nó hoạt động tương tự như [`unref()`](/api/reactivity-utilities.html#unref), nhưng với xử lý đặc biệt cho hàm.

Chú ý rằng `toValue(url)` được gọi **bên trong** callback của `watchEffect`. Điều này đảm bảo rằng bất kỳ phụ thuộc có thể được truy cập trong quá trình chuẩn hóa `toValue()` đều được theo dõi bởi watcher.

Phiên bản hiện tại của `useFetch()` bây giờ chấp nhận chuỗi URL tĩnh, refs và getters, làm cho nó linh hoạt hơn nhiều. Watch effect sẽ chạy ngay lập tức, và sẽ theo dõi bất kỳ phụ thuộc nào được truy cập trong `toValue(url)`. Nếu không có phụ thuộc nào được theo dõi (ví dụ: url đã là chuỗi), hiệu ứng chỉ chạy một lần; nếu không, nó sẽ chạy lại mỗi khi một phụ thuộc được theo dõi thay đổi.
This version of `useFetch()` now accepts static URL strings, refs, and getters, making it much more flexible. The watch effect will run immediately, and will track any dependencies accessed during `toValue(url)`. If no dependencies are tracked (e.g. url is already a string), the effect runs only once; otherwise, it will re-run whenever a tracked dependency changes.

Đây là [phiên bản cập nhật của `useFetch()`](https://play.vuejs.org/#eNptVMFu2zAM/RXOFztYZncodgmSYAPWnTZsKLadfFFsulHrSIZEJwuC/PtIyXaTtkALxxT5yPf45FPypevyfY/JIln6yumOwCP13bo0etdZR3ACh80cKrvresIaztA4u4OUi9KLpN7jN6RqO53nxRjKHz1nlqayxhNslMc/roUVpFuizi+K4tFb07Wqwq1ta3Q5HTtd2RpzblqQra0vGCCW65oreaIs/ZjOxmAf8MYRs2wGq/XU6D3X5HvV9sj5Y8UJakVqDuicdXMGJHfk0VcTj4wxOX9ZRFVYD34h3PGchPwG8N2qGjobZlpIYLnpiayB/YfGulWZaNAGPpUJfK5aXT1JRIbXZbI+nUDD+bwsYklAL2lZ6z1X64ZTw2CcKcAM3a1/2s6/gzsJAzKL3hA6rBfAWCE536H36gEDriwwFA4zTSMEpox7L8+L/pxacPv4K86Brcc4jGjFNV/5AS3TlrbLzqHwkLPYkt/fxFiLUto85Hk+ni+LScpknlwYhX147buD4oO7psGK5kD2r+zxhQdLg/9CSdObijSzvVoinGSeuPYwbPSP6VtZ8HgSJHx5JP8XA2TKH00F0V4BFaAouISvDHhiNrBB3j1CI90D5ZglfaMHuYXAx3Dc2+v4JbRt9wi0xWDymCpTbJ01tvftEbwFTakHcqp), với một độ trễ nhân tạo và lỗi ngẫu nhiên để minh họa.

## Những quy ước và cách vận dụng  {#conventions-and-best-practices}

### Đặt tên {#naming}

Theo như quy ước, hàm composable nên bắt đầu bằng "use" và sử dụng kiểu camelCase. Ví dụ: `useMouse`, `useFetch`, `useEventListener`.

### Input Arguments {#input-arguments}

Một composable có thể chấp nhận ref hoặc getter làm đối số đầu vào ngay cả khi nó không phụ thuộc vào chúng để tạo ra reactivity. Nếu bạn đang viết một composable có thể được sử dụng bởi các nhà phát triển khác, lý tưởng nhất là xử lý trường hợp đối số đầu vào là ref hoặc getter thay vì giá trị thô (raw). Hàm tiện ích [`toValue()`](/api/reactivity-utilities#tovalue) sẽ hữu ích cho mục đích này:

```js
import { toValue } from 'vue'

function useFeature(maybeRefOrGetter) {
  // Nếu maybeRefOrGetter là ref hoặc getter,
  // giá trị chuẩn hóa của nó sẽ được trả về.
  // Nếu không, nó sẽ trả về chính nó.
  const value = toValue(maybeRefOrGetter)
}
```

Nếu composable của bạn tạo ra hiệu ứng reactive khi đầu vào là một ref hoặc getter, hãy chắc chắn rằng bạn đã theo dõi ref / getter một cách rõ ràng với `watch()`, hoặc gọi `toValue()` bên trong `watchEffect()` để nó được theo dõi đúng cách.

Hàm [useFetch() đã được thảo luận trước đó](#async-state-example) cung cấp một ví dụ cụ thể về cách mà một composable có thể chấp nhận refs, getters và giá trị thô như đối số đầu vào.

### Giá trị trả về {#return-values}

Bạn có thể đã nhận thấy rằng chúng ta đã sử dụng `ref()` thay vì `reactive()` một cách độc quyền trong composables. Quy ước được khuyến nghị là composables luôn trả về một đối tượng đơn giản, không reactive chứa nhiều refs. Điều này cho phép nó được phân rã (destructure) trong các component trong khi vẫn giữ được tính reactive:

```js
// x và y là refs
const { x, y } = useMouse()
```

Việc trả về một đối tượng reactive từ một composable sẽ khiến phép phân rã (destructure) mất mất kết nối với trạng thái bên trong composable, trong khi refs sẽ giữ được kết nối đó.

Nếu bạn muốn sử dụng state được trả về từ composables như là các thuộc tính của một đối tượng, bạn có thể bọc đối tượng trả về với `reactive()` để refs được giải gỡ. Ví dụ:

```js
const mouse = reactive(useMouse())
// mouse.x is linked to original ref
// mouse.x được liên kết với ref gốc
console.log(mouse.x)
```

```vue-html
Vị trí của con trỏ là: {{ mouse.x }}, {{ mouse.y }}
```

### Tác động phụ {#side-effects}

Việc thực hiện tác động phụ (ví dụ: thêm DOM event listener hoặc lấy dữ liệu) trong composables là OK, nhưng hãy chú ý đến các quy tắc sau:

- Nếu bạn đang làm việc trên một ứng dụng sử dụng [Server-Side Rendering](/guide/scaling-up/ssr) (SSR), hãy chắc chắn thực hiện các tác động phụ cụ thể cho DOM trong các lifecycle hooks sau khi mount, ví dụ `onMounted()`. Những hooks này chỉ được gọi trong trình duyệt, vì vậy bạn có thể chắc chắn rằng mã bên trong chúng có quyền truy cập vào DOM.

- Hãy nhớ làm sạch tác động phụ trong `onUnmounted()`. Ví dụ, nếu một composable thiết lập một DOM event listener, nó nên xóa listener đó trong `onUnmounted()` như chúng ta đã thấy trong ví dụ `useMouse()`. Bạn cũng có thể sử dụng một composable tự động làm điều này cho bạn, như ví dụ `useEventListener()`.

### Các hạn chế khi sử dụng {#usage-restrictions}

Composables chỉ nên được gọi trong `<script setup>` hoặc `setup()` hook. Chúng cũng chỉ nên được gọi **đồng bộ** trong các ngữ cảnh này. Trong một số trường hợp, bạn cũng có thể gọi chúng trong các lifecycle hooks như `onMounted()`.

Những hạn chế này rất quan trọng vì đây là các ngữ cảnh mà Vue có thể xác định được instance component hiện tại. Việc truy cập vào instance component hiện tại là cần thiết để:

1. Lifecycle hooks có thể được đăng ký cho component.

2. Các thuộc tính computed và watcher có thể được liên kết với component, để chúng có thể được hủy khi instance bị unmounted để ngăn chặn rò rỉ bộ nhớ.

:::tip
`<script setup>` là nơi duy nhất mà bạn có thể gọi composables **sau** khi sử dụng `await`. Trình biên dịch sẽ tự động khôi phục ngữ cảnh instance hoạt động cho bạn sau khi hoạt động bất đồng bộ kết thúc.
:::

## Trích xuất Composables để Tổ chức mã {#extracting-composables-for-code-organization}

Composables không chỉ có thể được trích xuất để tái sử dụng, mà còn để tổ chức mã. Khi sự phức tạp của các component tăng lên, bạn có thể kết thúc với các component quá lớn để điều hướng và suy luận. Composition API cho phép bạn tổ chức mã component thành các hàm nhỏ dựa trên các vấn đề logic:

```vue
<script setup>
import { useFeatureA } from './featureA.js'
import { useFeatureB } from './featureB.js'
import { useFeatureC } from './featureC.js'

const { foo, bar } = useFeatureA()
const { baz } = useFeatureB(foo)
const { qux } = useFeatureC(baz)
</script>
```

Thậm chí, bạn cũng có thể nghĩ rằng những composable này như là là các "service" cho component của bạn và có thể giao tiếp với nhau.

## Sử dung Composables trong Options API {#using-composables-in-options-api}

Nếu bạn đang sử dụng Options API, composables phải được gọi bên trong `setup()`, và các bindings trả về phải được trả về từ `setup()` để chúng được tiết lộ cho `this` và template:

```js
import { useMouse } from './mouse.js'
import { useFetch } from './fetch.js'

export default {
  setup() {
    const { x, y } = useMouse()
    const { data, error } = useFetch('...')
    return { x, y, data, error }
  },
  mounted() {
    // các thuộc tính được trả về từ setup() có thể được truy cập trên `this`
    console.log(this.x)
  }
  // ...những option khác
}
```

## So sánh với các kỹ thuật khác {#comparisons-with-other-techniques}

### Đối với Mixins {#vs-mixins}

Những người dùng từ Vue 2 có thể quen với tùy chọn [mixins](/api/options-composition#mixins), mà cũng cho phép chúng ta trích xuất logic component thành các đơn vị có thể tái sử dụng. Có ba điểm chính mà mixins không phù hợp:

1. **Nguồn không rõ ràng của các thuộc tính**: khi sử dụng nhiều mixins, việc thuộc tính của instance nào được inject bởi mixin nào trở nên không rõ ràng, khiến cho nó khó để có thể truy dấu việc triển khai và hiểu được hành vi của component. Đây cũng là lý do tại sao chúng tôi khuyến nghị sử dụng pattern refs + destructure cho composables: nó làm cho nguồn của thuộc tính rõ ràng trong các component sử dụng mixin.

2. **Xung đột namespace**: nhiều mixins từ các tác giả khác nhau có thể đăng ký các key thuộc tính giống nhau, gây ra xung đột namespace. Với composables, bạn có thể đổi tên các biến destructured nếu có key xung đột từ các composable khác nhau.

3. **Giao tiếp cross-mixin ngầm**: nhiều mixins cần tương tác với nhau phải dựa vào các key thuộc tính chung, khiến chúng ngầm liên kết với nhau. Với composables, giá trị trả về từ một composable có thể được truyền vào một composable khác như là đối số, giống như các hàm thông thường.

Đối với những lý do trên, chúng tôi không còn khuyến nghị sử dụng mixins trong Vue 3. Tính năng này chỉ được giữ lại cho mục đích chuyển đổi và đã quen thuộc.

### Đối với Renderless Components {#vs-renderless-components}

Trong chương [component slots](/guide/components/slots), chúng ta đã thảo luận về pattern [Renderless Component](/guide/components/slots#renderless-components) dựa trên scoped slots. Chúng ta đã thậm chí triển khai lại cùng một ví dụ truy dấu chuột sử dụng renderless components.

Lợi ích chính của composables so với renderless components là composables không làm tăng chi phí vận hành của instance component. Khi sử dụng trên toàn bộ ứng dụng, số lượng instance component được tạo ra bởi pattern renderless component có thể trở thành một chi phí hiệu năng đáng kể.

Điều được khuyến nghị là sử dụng composables khi sử dụng logic thuần, và sử dụng component khi tái sử dụng cả logic và layout.

### Đối với React Hooks {#vs-react-hooks}

Nếu bạn có kinh nghiệm với React, bạn có thể nhận ra rằng composables rất giống với custom React hooks. Composition API được lấy cảm hứng từ React hooks, và Vue composables thực sự tương tự với React hooks trong khả năng kết hợp logic. Tuy nhiên, Vue composables dựa trên hệ thống reactivity tinh tế của Vue, mà cơ bản khác biệt so với mô hình thực thi của React hooks. Điều này được thảo luận chi tiết hơn trong [Composition API FAQ](/guide/extras/composition-api-faq#comparison-with-react-hooks).

## Đọc thêm {#further-reading}

- [Chuyên sâu về Reactivity](/guide/extras/reactivity-in-depth): dành cho những người muốn hiểu rõ hơn về cách hệ thống reactivity của Vue hoạt động ở low-level.
- [Quản lý State](/guide/scaling-up/state-management): một hướng dẫn chi tiết về cách quản lý state trong ứng dụng lớn.
- [Kiểm thử Composables](/guide/scaling-up/testing#testing-composables): các gợi ý cho việc kiểm thử composables.
- [VueUse](https://vueuse.org/): một thư viện chứa một bộ sưu tập các Vue composables. Mã nguồn của thư viện này cũng là một nguồn học tốt.
