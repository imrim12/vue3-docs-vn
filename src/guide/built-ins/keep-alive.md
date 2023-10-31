<script setup>
import SwitchComponent from './keep-alive-demos/SwitchComponent.vue'
</script>

# KeepAlive {#keepalive}

`<KeepAlive>` là một component tích hợp sẵn cho phép chúng ta lưu trữ các của instance component theo điều kiện khi chuyển đổi giữa các component khác nhau.

## Ứng dụng cơ bản {#basic-usage}

Trong chương [Component Basics](/guide/essentials/component-basics), chúng ta đã giới thiệu cú pháp cho [Dynamic Components](/guide/essentials/component-basics#dynamic-components), sử dụng phần tử đặc biệt `<component>`:

```vue-html
<component :is="activeComponent" />
```

Theo mặc định thì một instance component đang hoạt động sẽ bị unmount khi chuyển đổi sang component khác. Điều này sẽ khiến cho mọi state của nó bị mất. Khi component này được hiển thị lại, một instance mới sẽ được tạo ra với trạng thái ban đầu.

Trong ví dụ dưới đây, chúng ta có hai component stateful - A chứa một counter, trong khi B chứa một message được đồng bộ với một input thông qua `v-model`. Hãy thử cập nhật state của một trong chúng, chuyển đổi sang component khác, và sau đó chuyển đổi lại:

<SwitchComponent />

Bạn sẽ thấy rằng khi chuyển lại, trạng thái đã được thay đổi trước đó sẽ bị reset.

Tạo mới một instance component khi chuyển đổi là một hành vi bình thường, nhưng trong trường hợp này, chúng ta muốn giữ lại hai instance component ngay cả khi chúng không hoạt động. Để giải quyết vấn đề này, chúng ta có thể bọc dynamic component của chúng ta bằng phần tử `<KeepAlive>`:

```vue-html
<!-- Những component không hoạt động sẽ được cache! -->
<KeepAlive>
  <component :is="activeComponent" />
</KeepAlive>
```

Lúc này, state của component sẽ được lưu trữ khi chuyển đổi giữa các component khác nhau:

<SwitchComponent use-KeepAlive />

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNqtUsFOwzAM/RWrl4IGC+cqq2h3RFw495K12YhIk6hJi1DVf8dJSllBaAJxi+2XZz8/j0lhzHboeZIl1NadMA4sd73JKyVaozsHI9hnJqV+feJHmODY6RZS/JEuiL1uTTEXtiREnnINKFeAcgZUqtbKOqj7ruPKwe6s2VVguq4UJXEynAkDx1sjmeMYAdBGDFBLZu2uShre6ioJeaxIduAyp0KZ3oF7MxwRHWsEQmC4bXXDJWbmxpjLBiZ7DwptMUFyKCiJNP/BWUbO8gvnA+emkGKIgkKqRrRWfh+Z8MIWwpySpfbxn6wJKMGV4IuSs0UlN1HVJae7bxYvBuk+2IOIq7sLnph8P9u5DJv5VfpWWLaGqTzwZTCOM/M0IaMvBMihd04ruK+lqF/8Ajxms8EFbCiJxR8khsP6ncQosLWnWV6a/kUf2nqu75Fby04chA0iPftaYryhz6NBRLjdtajpHZTWPio=)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNqtU8tugzAQ/JUVl7RKWveMXFTIseofcHHAiawasPxArRD/3rVNSEhbpVUrIWB3x7PM7jAkuVL3veNJmlBTaaFsVraiUZ22sO0alcNedw2s7kmIPHS1ABQLQDEBAMqWvwVQzffMSQuDz1aI6VreWpPCEBtsJppx4wE1s+zmNoIBNLdOt8cIjzut8XAKq3A0NAIY/QNveFEyi8DA8kZJZjlGALQWPVSSGfNYJjVvujIJeaxItuMyo6JVzoJ9VxwRmtUCIdDfNV3NJWam5j7HpPOY8BEYkwxySiLLP1AWkbK4oHzmXOVS9FFOSM3jhFR4WTNfRslcO54nSwJKcCD4RsnZmJJNFPXJEl8t88quOuc39fCrHalsGyWcnJL62apYNoq12UQ8DLEFjCMy+kKA7Jy1XQtPlRTVqx+Jx6zXOJI1JbH4jejg3T+KbswBzXnFlz9Tjes/V/3CjWEHDsL/OYNvdCE8Wu3kLUQEhy+ljh+brFFu)

</div>

:::tip
Khi sử dụng trong [in-DOM templates](/guide/essentials/component-basics#in-dom-template-parsing-caveats), phần tử này nên được tham chiếu như `<keep-alive>`.
:::

## Include / Exclude {#include-exclude}

Theo mặc định, `<KeepAlive>` sẽ cache bất kỳ instance component nào được bọc bên trong nó. Chúng ta có thể tùy chỉnh hành vi này thông qua các props `include` và `exclude`. Cả hai props này có thể là một chuỗi phân tách bằng dấu phẩy, một `RegExp`, hoặc một mảng chứa các kiểu:

```vue-html
<!-- comma-delimited string -->
<KeepAlive include="a,b">
  <component :is="view" />
</KeepAlive>

<!-- regex (use `v-bind`) -->
<KeepAlive :include="/a|b/">
  <component :is="view" />
</KeepAlive>

<!-- Array (use `v-bind`) -->
<KeepAlive :include="['a', 'b']">
  <component :is="view" />
</KeepAlive>
```

Phần trùng khớp sẽ được đối chiếu với tên của component, được khai báo thông qua option [`name`](/api/options-misc#name), vì vậy các component cần được cache theo điều kiện bởi `KeepAlive` phải khai báo rõ ràng một option `name`.

:::tip
Từ phiên bản 3.2.34, một single-file component sử dụng `<script setup>` sẽ tự động suy ra option `name` dựa trên tên file, loại bỏ nhu cầu khai báo tên một cách thủ công.
:::

## Số Instances tối đa được cached {#max-cached-instances}

Chúng ta có thể giới hạn số lượng instance component được cache tối đa thông qua prop `max`. Khi `max` được chỉ định, `<KeepAlive>` sẽ hoạt động như một [LRU cache](<https://en.wikipedia.org/wiki/Cache_replacement_policies#Least_recently_used_(LRU)>): nếu số lượng instance component được cache sắp vượt quá số lượng tối đa được chỉ định, instance component được truy cập ít nhất sẽ bị hủy để tạo chỗ cho instance mới.

```vue-html
<KeepAlive :max="10">
  <component :is="activeComponent" />
</KeepAlive>
```

## Vòng đời của một instance khi được cached {#lifecycle-of-cached-instance}

Khi một instance component bị gỡ khỏi DOM nhưng vẫn là một phần của component tree được cache bởi `<KeepAlive>`, nó sẽ vào trạng thái **deactivated** thay vì bị unmount. Khi một instance component được chèn vào DOM như một phần của cached tree, nó sẽ được **activated**.

<div class="composition-api">

Một component `kept-alive` có thể đăng ký lifecycle hooks cho hai trạng thái này thông qua việc sử dụng [`onActivated()`](/api/composition-api-lifecycle#onactivated) và [`onDeactivated()`](/api/composition-api-lifecycle#ondeactivated):

```vue
<script setup>
import { onActivated, onDeactivated } from 'vue'

onActivated(() => {
  // được gọi khi component được mount lần đầu tiên
  // và mỗi lần nó được chèn vào từ cache
})

onDeactivated(() => {
  // được gọi khi component bị gỡ khỏi DOM vào cache
  // và cũng được gọi khi component bị unmount
})
</script>
```

</div>
<div class="options-api">

Một kept-alive component có thể đăng ký lifecycle hooks cho hai trạng thái này thông qua việc sử dụng [`activated`](/api/options-lifecycle#activated) và [`deactivated`](/api/options-lifecycle#deactivated):

```js
export default {
  activated() {
    // được gọi khi component được mount lần đầu tiên
    // và mỗi lần nó được chèn vào từ cache
  },
  deactivated() {
    // được gọi khi component bị gỡ khỏi DOM vào cache
    // và cũng được gọi khi component bị unmount
  }
}
```

</div>

Lưu ý rằng:

- <span class="composition-api">`onActivated`</span><span class="options-api">`activated`</span> cũng được gọi khi component được mount, và <span class="composition-api">`onDeactivated`</span><span class="options-api">`deactivated`</span> được gọi khi component bị unmount.

- Cả hai hook này không chỉ hoạt động cho component gốc được cache bởi `<KeepAlive>`, mà còn cho các component con trong cây được cache.

---

**Tham khảo thêm**

- [Tham khảo `<KeepAlive>` API](/api/built-in-components#keepalive)
