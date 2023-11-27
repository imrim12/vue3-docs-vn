# Cú pháp Template {#template-syntax}

Vue sử dụng cú pháp template dựa trên HTML cho phép khai báo bạn ràng buộc DOM đã được render với các dữ liệu của component instance cơ sở. Tất cả các template Vue đều là HTML hợp lệ và có thể được phân tích bởi các trình duyệt và trình phân tích HTML tuân thủ quy chuẩn.

Bên dưới mã nguồn, Vue biên dịch các template thành mã JavaScript đã được tối ưu hóa cao. Kết hợp với hệ thống reactivity, Vue có thể tìm ra số lượng tối thiểu các component để re-render và áp dụng số lượng tối thiểu các thao tác DOM khi trạng thái ứng dụng thay đổi.

Nếu bạn đã quen với các khái niệm Virtual DOM và thích sức mạnh của JavaScript, bạn cũng có thể [viết trực tiếp các hàm render](/guide/extras/render-function) thay vì các template, với hỗ trợ JSX tùy chọn. Tuy nhiên, hãy lưu ý rằng chúng không có cùng mức độ tối ưu hóa thời gian biên dịch như các template.

## Văn bản nội suy {#text-interpolation}

Cách đơn giản nhất để ràng buộc dữ liệu là sử dụng văn bản nội suy sử dụng cú pháp "Mustache" (ngoặc nhọn kép):

```vue-html
<span>Message: {{ msg }}</span>
```

Thẻ mustache sẽ được thay thế bằng giá trị của thuộc tính `msg` [từ thể hiện component instance tương ứng](/guide/essentials/reactivity-fundamentals#declaring-reactive-state). Nó cũng sẽ được cập nhật mỗi khi thuộc tính `msg` thay đổi.

## HTML thuần {#raw-html}

Các mustache xem các dữ liệu như là văn bản thuần, không phải HTML. Để xuất HTML thực sự, bạn sẽ cần sử dụng [`v-html` directive](/api/built-in-directives#v-html):

```vue-html
<p>Sử dụng văn bản nội suy: {{ rawHtml }}</p>
<p>Sử dụng v-html directive: <span v-html="rawHtml"></span></p>
```

<script setup>
  const rawHtml = '<span style="color: red">Dòng này nên có màu đỏ.</span>'
</script>

<div class="demo">
  <p>Sử dụng văn bản nội suy: {{ rawHtml }}</p>
  <p>Sử dụng v-html directive: <span v-html="rawHtml"></span></p>
</div>

Thuộc tính `v-html` bạn đang thấy được gọi là **directive**. Các directive được tiền tố bằng `v-` để chỉ ra rằng chúng là các thuộc tính đặc biệt được cung cấp bởi Vue, và như bạn có thể đoán được, chúng áp dụng hành vi reactive đặc biệt cho DOM đã được render. Ở đây, chúng ta đang đơn giản chỉ nói "giữ cho phần tử này HTML bên trong được cập nhật với thuộc tính `rawHtml` trên instance hiện tại."

Nội dung của `span` sẽ được thay thế bằng giá trị của thuộc tính `rawHtml`, được hiểu là HTML thuần - các ràng buộc dữ liệu bị bỏ qua. Lưu ý rằng bạn không thể sử dụng `v-html` để tạo các template nhỏ hơn, vì Vue không phải là một trình biên dịch mẫu dựa trên chuỗi. Thay vào đó, các component được ưu tiên là đơn vị cơ bản cho việc tái sử dụng và composition.

:::warning Cảnh báo bảo mật
Việc render HTML động tuỳ ý trên trang web của bạn có thể rất nguy hiểm vì nó có thể dẫn đến các lỗ hổng [XSS](https://en.wikipedia.org/wiki/Cross-site_scripting). Chỉ sử dụng `v-html` trên nội dung được tin tưởng và **không bao giờ** sử dụng trên nội dung được cung cấp bởi người dùng.
:::

## Ràng buộc thuộc tính {#attribute-bindings}

Mustache không thể được sử dụng trong các thuộc tính HTML. Thay vào đó, hãy sử dụng [`v-bind` directive](/api/built-in-directives#v-bind):

```vue-html
<div v-bind:id="dynamicId"></div>
```

`v-bind` directive chỉ cho Vue biết rằng nễn giữ cho thuộc tính `id` của phần tử này được cập nhật với giá trị của thuộc tính `dynamicId` trong component. Nếu giá trị ràng buộc là `null` hoặc `undefined`, thuộc tính sẽ bị xóa khỏi phần tử đã render.

### Viết tắt {#shorthand}

Bởi vì `v-bind` thường xuyên được sử dụng, nó có một cú pháp viết tắt riêng:

```vue-html
<div :id="dynamicId"></div>
```

Thuộc tính bắt đầu với `:` có thể trông hơi khác so với HTML bình thường, nhưng thực tế đó là một ký tự hợp lệ cho tên thuộc tính và tất cả các trình duyệt được hỗ trợ bởi Vue có thể phân tích nó đúng. Ngoài ra, chúng không xuất hiện trong markup cuối cùng được render. Cú pháp viết tắt là tùy chọn, nhưng bạn sẽ có thể đánh giá cao nó khi bạn tìm hiểu thêm về cách sử dụng sau này.

> Trong phần còn lại của hướng dẫn, chúng tôi sẽ sử dụng cú pháp viết tắt trong các ví dụ mã, vì đó là cách sử dụng phổ biến nhất cho các nhà phát triển Vue.

### Thuộc tính Boolean {#boolean-attributes}

[Thuộc tính Boolean](https://html.spec.whatwg.org/multipage/common-microsyntaxes.html#boolean-attributes) là các thuộc tính có thể chỉ ra giá trị true / false bằng sự hiện diện của chúng trên một phần tử. Ví dụ, [`disabled`](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/disabled) là một trong những thuộc tính Boolean được sử dụng phổ biến nhất.

`v-bind` hoạt động hơi khác trong trường hợp này:

```vue-html
<button :disabled="isButtonDisabled">Button</button>
```

Thuộc tính `disabled` sẽ được thêm vào nếu `isButtonDisabled` có một [giá trị truthy](https://developer.mozilla.org/en-US/docs/Glossary/Truthy). Nó cũng sẽ được bao gồm nếu giá trị là một chuỗi rỗng, duy trì tính nhất quán với `<button disabled="">`. Đối với các giá trị [falsy](https://developer.mozilla.org/en-US/docs/Glossary/Falsy) khác, thuộc tính sẽ bị bỏ qua.

### Ràng buộc động các thuộc tính {#dynamically-binding-multiple-attributes}

Nếu bạn có một JavaScript object thể hiện nhiều thuộc tính như sau:

<div class="composition-api">

```js
const objectOfAttrs = {
  id: 'container',
  class: 'wrapper'
}
```

</div>
<div class="options-api">

```js
data() {
  return {
    objectOfAttrs: {
      id: 'container',
      class: 'wrapper'
    }
  }
}
```

</div>

Bạn có thể ràng buộc chúng với một phần tử bằng cách sử dụng `v-bind` như sau:

```vue-html
<div v-bind="objectOfAttrs"></div>
```

## Sử dụng biểu thức JavaScript {#using-javascript-expressions}

Cho đến hiện tại, chúng ta chỉ ràng buộc với các thuộc tính đơn giản trong các template của chúng ta. Nhưng Vue có hỗ trợ đầy đủ toàn bộ sức mạnh của các biểu thức JavaScript bên trong tất cả các ràng buộc dữ liệu:

```vue-html
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div :id="`list-${id}`"></div>
```

Các biểu thức này sẽ được đánh giá là JavaScript trong phạm vi dữ liệu của instance component hiện tại.

Trong Vue template, các biểu thức JavaScript có thể được sử dụng ở các vị trí sau:

- Trong các văn bản nội suy (mustaches)
- Trong giá trị thuộc tính: `v-bind:id="someDynamicId"` (thuộc tính đặc biệt bắt đầu với `v-`)

### Chỉ sử dụng biểu thức {#expressions-only}

Mỗi ràng buộc chỉ có thể chứa **một biểu thức**. Một biểu thức là một đoạn mã có thể được đánh giá thành một giá trị. Một kiểm tra đơn giản là xem nó có thể được sử dụng sau `return` hay không.

Do đó, các biểu thức sau đây đều **không** hoạt động:

```vue-html
<!-- đây là một câu lệnh, không phải là một biểu thức: -->
{{ var a = 1 }}

<!-- kiểm tra điều kiện không phải là một biểu thức, hãy sử dụng ternary operator thay thế: -->
{{ if (ok) { return message } }}
```

### Gọi hàm {#calling-functions}

Có thể gọi hàm được exposed bởi một component trong các biểu thức template:

It is possible to call a component-exposed method inside a binding expression:

```vue-html
<time :title="toTitleDate(date)" :datetime="date">
  {{ formatDate(date) }}
</time>
```

:::tip
Hàm được gọi trong các biểu thức ràng buộc sẽ được gọi mỗi khi component được render lại. Do đó, chúng **không** nên có bất kỳ tác động phụ nào, chẳng hạn như thay đổi dữ liệu hoặc kích hoạt các hoạt động bất đồng bộ.
:::

### Hạn chế truy cập toàn cục {#restricted-globals-access}

Biểu thức template được sandbox và chỉ có quyền truy cập vào [danh sách biến cục bộ bị hạn chế](https://github.com/vuejs/core/blob/main/packages/shared/src/globalsAllowList.ts#L3). Danh sách này bao gồm các biến toàn cục được tích hợp sẵn thông dụng như `Math` và `Date`.

Các biến toàn cục không được bao gồm trong danh sách, ví dụ: `window` và `document` sẽ không được truy cập trong các biểu thức template. Tuy nhiên, bạn có thể, định nghĩa các biến toàn cục bổ sung cho tất cả các biểu thức Vue bằng cách thêm chúng vào [`app.config.globalProperties`](/api/application#app-config-globalproperties).

## Directives {#directives}

Directives là các thuộc tính đặc biệt với tiền tố `v-`. Vue cung cấp một số [built-in directives](/api/built-in-directives), bao gồm `v-html` và `v-bind` mà chúng tôi đã giới thiệu ở trên.

Giá trị của các thuộc tính directive được mong đợi là một biểu thức JavaScript (ngoại trừ `v-for`, `v-on` và `v-slot`, sẽ được thảo luận trong các phần tương ứng của chúng sau). Công việc của một directive là áp dụng các cập nhật phản ứng vào DOM khi giá trị của biểu thức của nó thay đổi. Hãy xem [`v-if`](/api/built-in-directives#v-if) là một ví dụ:

```vue-html
<p v-if="seen">Now you see me</p>
```

Ở đây, `v-if` directive sẽ loại bỏ / chèn phần tử `<p>` dựa trên giá trị Truthy/Falsy của biểu thức `seen`.

### Đối số {#arguments}

Một số directive có thể nhận một "đối số", được chỉ định bằng cách đặt chúng sau dấu chấm. Ví dụ, `v-bind` directive có thể được sử dụng để cập nhật một thuộc tính HTML:

```vue-html
<a v-bind:href="url"> ... </a>

<!-- viết tắt -->
<a :href="url"> ... </a>
```

Tại đây, `href` là đối số, nói với directive `v-bind` để ràng buộc thuộc tính `href` của phần tử với giá trị của biểu thức `url`. Trong cú pháp rút gọn, mọi thứ trước đối số (tức là `v-bind:`) được rút gọn thành một ký tự duy nhất, `:`.

Một ví dụ khác là `v-on` directive, được dùng để lắng nghe các sự kiện DOM:

```vue-html
<a v-on:click="doSomething"> ... </a>

<!-- viết tắt -->
<a @click="doSomething"> ... </a>
```

Tại đây, đối số là tên sự kiện để lắng nghe: `click`. `v-on` có một rút gọn tương ứng, đó là ký tự `@`. Chúng tôi sẽ nói về xử lý sự kiện chi tiết hơn.

### Đối số động {#dynamic-arguments}

Chúng ta có thể sử dụng các biểu thức JavaScript trong các đối số directive bằng cách bao chúng trong dấu ngoặc vuông:

```vue-html
<!--
Lưu ý rằng có một số ràng buộc cho biểu thức đối số,
như được giải thích trong các phần "Ràng buộc giá trị cho đối số động" và "Ràng buộc cú pháp cho đối số động" bên dưới.
-->
<a v-bind:[attributeName]="url"> ... </a>

<!-- viết tắt -->
<a :[attributeName]="url"> ... </a>
```

Tại đây, `attributeName` sẽ được đánh giá là một biểu thức JavaScript, và giá trị của nó sẽ được sử dụng làm tên thuộc tính. Ví dụ, nếu instance component của bạn có một thuộc tính `attributeName` với giá trị `"href"`, thì directive này sẽ được tương đương với `v-bind:href`.

Tương tự, bạn cũng có thể sử dụng các biểu thức JavaScript để ràng buộc một sự kiện lắng nghe động:

```vue-html
<a v-on:[eventName]="doSomething"> ... </a>

<!-- viết tắt -->
<a @[eventName]="doSomething">
```

Trong ví dụ này, khi giá trị của `eventName` là `"focus"`, `v-on:[eventName]` sẽ tương đương với `v-on:focus`.

#### Ràng buộc giá trị cho đối số động {#dynamic-argument-value-constraints}

Các đối số động được biến đổi thành chuỗi, ngoại trừ `null`. Giá trị đặc biệt `null` có thể được sử dụng để xóa ràng buộc. Bất kỳ giá trị khác không phải là chuỗi sẽ kích hoạt một cảnh báo.

#### Ràng buộc cú pháp cho đối số động {#dynamic-argument-syntax-constraints}

Các biểu thức động sở hữu một số ràng buộc cú pháp vì một số ký tự, chẳng hạn như khoảng trắng và dấu ngoặc vuông, không hợp lệ trong attribute name của HTML. Ví dụ:

```vue-html
<!-- Việc này sẽ kích hoạt một cảnh báo từ trình biên dịch. -->
<a :['foo' + bar]="value"> ... </a>
```

Nếu bạn cần truyền một biểu thức phức tạp thì việc sử dụng một [computed property](./computed) thường sẽ tốt hơn, chúng ta sẽ tìm hiểu về `computed` sau.

Khi sử dụng các temlate trong DOM (template trực tiếp được viết trong file HTML), bạn cũng nên tránh đặt tên các key với các ký tự viết hoa, vì các trình duyệt sẽ ép buộc tên thuộc tính thành chữ thường:

```vue-html
<a :[someAttr]="value"> ... </a>
```

Ví dụ trên sẽ được chuyển đổi thành `:[someattr]` trong các template trong DOM. Nếu component của bạn có một thuộc tính `someAttr` thay vì `someattr`, mã của bạn sẽ không hoạt động. Các template trong Single-File Components **không** bị ràng buộc bởi ràng buộc này.

### Modifiers {#modifiers}

Modifier là các hậu tố đặc biệt được đánh dấu bằng một dấu chấm, chỉ ra rằng một directive nên được ràng buộc theo một cách đặc biệt nào đó. Ví dụ, modifier `.prevent` cho biết directive `v-on` nên gọi `event.preventDefault()` trên sự kiện đã kích hoạt:

```vue-html
<form @submit.prevent="onSubmit">...</form>
```

Bạn sẽ thấy các ví dụ khác về modifiers sau, [cho `v-on`](./event-handling#event-modifiers) và [cho `v-model`](./forms#modifiers), khi chúng ta khám phá các tính năng đó.

Và cuối cùng là là một biểu đồ cú pháp đầy đủ của các directive:

![directive syntax graph](./images/directive.png)

<!-- https://www.figma.com/file/BGWUknIrtY9HOmbmad0vFr/Directive -->
