# Plugins {#plugins}

## Giới thiệu {#introduction}

Plugins là các đoạn mã tự chứa thường thêm các chức năng cấp ứng dụng vào Vue. Đây là cách cài đặt một plugin:

```js
import { createApp } from 'vue'

const app = createApp({})

app.use(myPlugin, {
  /* các tuỳ chọn khác */
})
```

Một plugin được định nghĩa như một đối tượng chứa một phương thức `install()`, hoặc đơn giản là một hàm hoạt động như một phương thức cài đặt. Phương thức cài đặt nhận vào [đối tượng ứng dụng](/api/application) cùng với các tuỳ chọn bổ sung được truyền vào `app.use()`, nếu có:

```js
const myPlugin = {
  install(app, options) {
    // tuỳ chỉnh app
  }
}
```

Không có phạm vi cụ thể cho một plugin, nhưng các trường hợp phổ biến mà plugin hữu ích bao gồm:

1. Đăng ký một hoặc nhiều components toàn cục hoặc custom directives với [`app.component()`](/api/application#app-component) và [`app.directive()`](/api/application#app-directive).

2. Tạo một resource [injectable](/guide/components/provide-inject) trong toàn bộ ứng dụng bằng cách gọi [`app.provide()`](/api/application#app-provide).

3. Thêm một số thuộc tính hoặc phương thức toàn cục bằng cách gắn chúng vào [`app.config.globalProperties`](/api/application#app-config-globalproperties).

4. Một thư viện cần thực hiện một số kết hợp của các điều trên (ví dụ: [vue-router](https://github.com/vuejs/vue-router-next)).

## Viết một Plugin {#writing-a-plugin}

Để hiểu rõ hơn cách tạo plugin Vue.js của riêng bạn, chúng ta sẽ tạo một phiên bản đơn giản của plugin hiển thị các chuỗi `i18n` (viết tắt của [Internationalization](https://en.wikipedia.org/wiki/Internationalization_and_localization)).

Bắt đầu bằng cách thiết lập đối tượng plugin. Bạn nên tạo nó trong một tệp riêng và xuất nó như dưới đây để giữ logic được tách biệt.

```js
// plugins/i18n.js
export default {
  install: (app, options) => {
    // Plugin code sẽ được viết ở đây
  }
}
```

Chúng ta muốn tạo một hàm dịch. Hàm này sẽ nhận một chuỗi `key` chia cách bằng dấu chấm, chúng ta sẽ sử dụng nó để tìm chuỗi dịch trong các tuỳ chọn mà người dùng cung cấp. Đây là cách sử dụng dự kiến trong các template:

```vue-html
<h1>{{ $translate('greetings.hello') }}</h1>
```

Vì hàm này nên được toàn cục trong tất cả các template, chúng ta sẽ làm cho nó trở nên toàn cục bằng cách gắn nó vào `app.config.globalProperties` trong plugin của chúng ta:

```js{4-11}
// plugins/i18n.js
export default {
  install: (app, options) => {
    // inject một phương thức $translate() toàn cục
    app.config.globalProperties.$translate = (key) => {
      // lấy một thuộc tính lồng trong `options`
      // sử dụng `key` như là đường dẫn
      return key.split('.').reduce((o, i) => {
        if (o) return o[i]
      }, options)
    }
  }
}
```

Hàm `$translate` của chúng ta sẽ nhận một chuỗi như `greetings.hello`, tìm kiếm trong cấu hình do người dùng cung cấp và trả về giá trị dịch.

Object chứa các khoá dịch nên được truyền vào plugin trong quá trình cài đặt thông qua các tham số bổ sung cho `app.use()`:

```js
import i18nPlugin from './plugins/i18n'

app.use(i18nPlugin, {
  greetings: {
    hello: 'Bonjour!'
  }
})
```

Bây giờ, biểu thức ban đầu `$translate('greetings.hello')` sẽ được thay thế bằng `Bonjour!` tại thời gian chạy.

Xem thêm: [Augmenting Global Properties](/guide/typescript/options-api#augmenting-global-properties) <sup class="vt-badge ts" />

:::tip
Sử dụng các thuộc tính toàn cục một cách cẩn thận, vì nó có thể dễ dàng trở nên rối rắm nếu quá nhiều thuộc tính toàn cục được inject bởi các plugin khác nhau được sử dụng trong toàn bộ ứng dụng.
:::

### Provide / Inject với Plugins {#provide-inject-with-plugins}

Plugins cũng cho phép chúng ta sử dụng `inject` để cung cấp một hàm hoặc thuộc tính cho người dùng của plugin. Ví dụ, chúng ta có thể cho phép ứng dụng có quyền truy cập vào tham số của plugin để có thể sử dụng đối tượng dịch.

```js{10}
// plugins/i18n.js
export default {
  install: (app, options) => {
    app.provide('i18n', options)
  }
}
```

Người sử dụng plugin bây giờ đã có thể inject các tuỳ chọn của plugin vào các component của họ bằng cách sử dụng khoá `i18n`:

<div class="composition-api">

```vue
<script setup>
import { inject } from 'vue'

const i18n = inject('i18n')

console.log(i18n.greetings.hello)
</script>
```

</div>
<div class="options-api">

```js
export default {
  inject: ['i18n'],
  created() {
    console.log(this.i18n.greetings.hello)
  }
}
```

</div>
