# Thuật ngữ {#glossary}

Danh sách này nhằm để giải thích ý nghĩa của một số thuật ngữ thông dụng được sử dụng trong Vue. Nó được dùng để mô tả cách sử dụng các thuật ngữ, không phải là quy định về cách sử dụng chúng. Một số thuật ngữ có thể có ý nghĩa khác nhau hoặc có sự khác biệt nhỏ tùy thuộc vào ngữ cảnh.

[[TOC]]

## async component {#async-component}

*Async component* là một hàm bao bọc cho một component khác, cho phép component đó được lazy loaded. Việc này thường được sử dụng như một cách để làm giảm kích thước của các tệp `.js` sau khi build, cho phép chúng chia thành các phần nhỏ (chunks) và chỉ được tải khi cần thiết.

Vue Router có một tính năng tương tự cho việc [lazy loading của các component route](https://router.vuejs.org/guide/advanced/lazy-loading.html), tuy nhiên nó không sử dụng tính năng async components của Vue.

Để biết thêm chi tiết, xem:
- [Hướng dẫn - Async Components](/guide/components/async.html)

## compiler macro {#compiler-macro}

*Compiler macro* là một đoạn mã đặc biệt được xử lý bởi trình biên dịch và được chuyển thành một 'cái gì đó khác'. Nó thực chất là một dạng thay thế chuỗi nhưng 'thông minh hơn'.

Trình biên dịch của Vue [SFC](#single-file-component) hỗ trợ nhiều macro khác nhau, như `defineProps()`, `defineEmits()` và `defineExpose()`. Những macro này được thiết kế giống như các hàm JavaScript thông thường để chúng có thể hoạt động trong các công cụ phân tích cú pháp và suy luận kiểu dữ liệu JavaScript / TypeScript. Tuy nhiên, chúng không phải là các hàm thực sự được chạy trong trình duyệt. Đây là các chuỗi đặc biệt mà trình biên dịch phát hiện và thay thế bằng mã JavaScript thực sự sẽ được chạy.

Macro có một số hạn chế trong công dụng mà vốn không thể áp dụng cho mã JavaScript thông thường. Ví dụ, bạn sẽ có thể nghĩ rằng `const dp = defineProps` sẽ cho phép bạn tạo một bí danh `dp` cho `defineProps`, nhưng thực tế nó sẽ gây ra lỗi. Ngoài ra, cũng có những hạn chế về các giá trị có thể được truyền cho `defineProps()`, vì các 'tham số' phải được xử lý bởi trình biên dịch và không phải tại thời điểm chạy.

Để biết thêm chi tiết, xem:
- [`<script setup>` - `defineProps()` & `defineEmits()`](/api/sfc-script-setup.html#defineprops-defineemits)
- [`<script setup>` - `defineExpose()`](/api/sfc-script-setup.html#defineexpose)

## component {#component}

Thuật ngữ *component* không chỉ được sử dụng trong Vue mà nó còn rất thông dụng với nhiều UI frameworks khác nhau. Nó mô tả một phần của UI, ví dụ như một button hoặc checkbox. Các component cũng có thể được kết hợp với nhau để tạo thành các component lớn hơn.

Component là cơ chế chính được Vue cung cấp để chia nhỏ một UI thành các phần nhỏ hơn, cả để cải thiện khả năng bảo trì và cho phép tái sử dụng mã.

Vue component là một object. Mọi thuộc tính đều là tùy chọn, nhưng một trong hai thuộc tính `template` hoặc `render` bắt buộc phải có để component có thể render. Ví dụ, object sau đây sẽ là một component hợp lệ:

```js
const HelloWorldComponent = {
  render() {
    return 'Hello world!'
  }
}
```

Trong thực tế, đa số các ứng dụng Vue được viết bằng [Single-File Components](#single-file-component) (tệp `.vue`). Trong khi các component này có vẻ không phải là object khi nhìn lướt qua, trình biên dịch SFC sẽ biến chúng thành một object, được sử dụng như là giá trị mặc định của tệp. Nhìn từ góc độ bên ngoài, một tệp `.vue` chỉ là một ES module xuất ra một object component.

Thuộc tính của compononent object thường được gọi là *options*. Đây là lý do mà [Options API](#options-api) được đặt tên.

Các option của component định nghĩa cách các instance của component đó sẽ được tạo ra. Component được coi là tương tự như các class, mặc dù Vue không sử dụng các class JavaScript thực sự để định nghĩa chúng.

Thuật ngữ component còn có thể được sử dụng như một cách để chỉ các instance của component.

Để biết thêm chi tiết, xem:
- [Hướng dẫn - Cơ bản về Component](/guide/essentials/component-basics.html)

Từ khóa 'component' còn được xuất hiện trong một số thuật ngữ khác:
- [async component](#async-component)
- [dynamic component](#dynamic-component)
- [functional component](#functional-component)
- [Web Component](#web-component)

## composable {#composable}

Thuật ngữ *composable* mô tả một pattern thường được sử dụng trong Vue. Nó không phải là một tính năng riêng biệt của Vue, nó chỉ là một cách sử dụng lại [Composition API](#composition-api).

* Composable là một function.
* Composable được sử dụng để đóng gói và tái sử dụng logic có trạng thái.
* Tên của function thường bắt đầu bằng `use`, để các developer khác biết đó là một composable.
* Function thường được gọi trong quá trình đồng bộ trong hàm `setup()` của component (hoặc tương đương, trong quá trình thực thi của khối `<script setup>`). Điều này liên kết việc gọi composable với ngữ cảnh của component hiện tại, ví dụ thông qua việc gọi `provide()`, `inject()` hoặc `onMounted()`.
* Composable thường trả về một plain object, không phải là một reactive object. Object này thường chứa các refs và functions và được dự kiến gọi đến thông qua phương thức destructure.

Cũng như những pattern khác, có thể có một số sự bất đồng về việc một đoạn code cụ thể có thể được gọi là composable hay không. Không phải tất cả các utility function của JavaScript đều là composables. Nếu một function không sử dụng Composition API thì nó có thể không phải là một composable. Nếu nó không được gọi trong quá trình đồng bộ của `setup()` thì nó có thể không phải là một composable. Composable được sử dụng để đóng gói logic có trạng thái, chúng không phải chỉ là một quy ước đặt tên cho function.

Xem [Hướng dẫn - Composables](/guide/reusability/composables.html) để biết thêm chi tiết về composables.

## Composition API {#composition-api}

Thuật ngữ *Composition API* dùng để chỉ một tập hợp các function được sử dụng để viết component và composables trong Vue.

Thuật ngữ này cũng được dùng để chỉ một trong hai kiểu viết component, cùng với [Options API](#options-api). Component được viết bằng Composition API sử dụng một trong hai cách sau: `<script setup>` hoặc dùng riêng một hàm `setup()`.

Xem [Composition API FAQ](/guide/extras/composition-api-faq) để biết thêm chi tiết.

## custom element {#custom-element}

*custom element* là một tính năng của [Web Components](#web-component), được hỗ trợ trong các trình duyệt hiện đại. Nó liên quan đến việc sử dụng một custom HTML element trong markup HTML để có thể tạo ra một Web Component tại một vị trí trong trang.

Vue có hỗ trợ render các custom element và cho phép chúng được sử dụng trực tiếp trong template của component Vue.

Không nên nhầm lẫn custom element với khả năng sử dụng Vue component như một thẻ trong template của một Vue component khác. Custom element được sử dụng để tạo ra Web Component, không phải Vue component.

Để biết thêm chi tiết, xem:
- [Hướng dẫn - Vue và Web Components](/guide/extras/web-components.html)

## directive {#directive}

Thuật ngữ *directive* được sử dụng để chỉ các thuộc tính của template bắt đầu bằng tiền tố `v-`, hoặc các thuộc tính ngắn gọn tương đương.

Các directive được tích hợp sẵn trong Vue bao gồm `v-if`, `v-for`, `v-bind`, `v-on` và `v-slot`.

Vue cũng hỗ trợ việc tạo ra các directive tùy chỉnh, tuy nhiên chúng thường chỉ được sử dụng như một 'cửa thoát' để có thể trực tiếp thao tác các DOM node. Directive tùy chỉnh thường không được sử dụng để tái tạo lại các chức năng của các directive được tích hợp sẵn.

Để biết thêm chi tiết, xem:
- [Hướng dẫn - Template Syntax - Directives](/guide/essentials/template-syntax.html#directives)
- [Hướng dẫn - Directives tùy chỉnh](/guide/reusability/custom-directives.html)

## dynamic component {#dynamic-component}

Thuật ngữ *dynamic component* được sử dụng để mô tả các trường hợp mà việc lựa chọn component con để render phải được thực hiện một cách dynamically. Thông thường, điều này được thực hiện bằng cách sử dụng `<component :is="type">`.

Một dynamic component không phải là một loại component đặc biệt. Bất kỳ component nào cũng có thể được sử dụng như một dynamic component. Component có thể được thiết lập để dynamic, không phải bản thân component là dynamic.

Để biết thêm chi tiết, xem:
- [Hướng dẫn - Cơ bản về Components- Dynamic Components](/guide/essentials/component-basics.html#dynamic-components)

## effect {#effect}

Xem [reactive effect](#reactive-effect) và [side effect](#side-effect).

## event {#event}

Events được sử dụng để giao tiếp giữa các phần khác nhau của một program. Trong Vue, thuật ngữ này thường được áp dụng cho cả các event của các phần tử HTML cũng như các event của component Vue. Directive `v-on` được sử dụng trong template để lắng nghe cả hai loại event này.

Để biết thêm chi tiết, xem:
- [Hướng dẫn - Xử lý Event](/guide/essentials/event-handling.html)
- [Hướng dẫn - Component Events](/guide/components/events.html)

## fragment {#fragment}

Thuật ngữ *fragment* được sử dụng để chỉ một loại [VNode](#vnode) đặc biệt, được sử dụng như một parent cho các VNode khác, nhưng nó không render ra bất kỳ phần tử nào.

Tên của nó xuất phát từ khái niệm tương tự của [`DocumentFragment`](https://developer.mozilla.org/en-US/docs/Web/API/DocumentFragment) trong DOM API.

Fragment được sử dụng để hỗ trợ các component có nhiều root node. Trong khi các component này có thể có nhiều root node, bên trong chúng chỉ có một fragment node được sử dụng như một root duy nhất, như một parent của các node 'root'.

Fragment cũng được sử dụng bởi trình biên dịch template như một cách để bao bọc nhiều node động, ví dụ như các node được tạo ra thông qua `v-for` hoặc `v-if`. Việc này cho phép truyền thêm các thông tin bổ sung cho thuật toán patching của [VDOM](#virtual-dom). Phần lớn các thông tin này được xử lý nội bộ, nhưng bạn có thể bắt gặp nó trực tiếp khi sử dụng thuộc tính `key` trên thẻ `<template>` với `v-for`. Trong trường hợp này, `key` sẽ được thêm vào như một [prop](#prop) cho fragment VNode.

Fragment node hiện đang được render vào DOM dưới dạng các text node trống, tuy nhiên đây chỉ là một hình thức áp dụng (implementation detail). Bạn có thể gặp các text node này nếu bạn sử dụng `$el` hoặc cố gắng duyệt qua DOM với các API tích hợp sẵn trong trình duyệt.

## functional component {#functional-component}

Một component được định nghĩa thông qua một function được gọi là *functional component*. Mặc dù nó trông không giống vậy nếu bạn sử dụng `<script setup>`, nhưng component được export từ tệp `.vue` vẫn là một object.

Một *functional component* là một dạng thay thế cho component thông thường. Function này hoạt động như một [render function](#render-function) cho component.

Một functional component không thể có bất kỳ state nào của riêng nó. Nó cũng không đi qua các lifecycle hook của component, vậy nên bạn không thể sử dụng hook trong functional component. Điều này khiến chúng nhẹ hơn một chút so với các component thông thường.

Để biết thêm chi tiết, xem:
- [Hướng dẫn - Render Functions & JSX - Functional Components](/guide/extras/render-function.html#functional-components)

## hoisting {#hoisting}

Thuật ngữ *hoisting* được sử dụng để mô tả việc chạy một đoạn mã trước khi nó được đọc đến. Việc thực thi đoạn mã được 'kéo lên' một điểm sớm hơn bình thường.

JavaScript sử dụng hoisting cho một số cấu trúc, như `var`, `import` và khai báo hàm.

Trong Vue, trình biên dịch template áp dụng *static hoisting* để cải thiện hiệu suất. Khi chuyển đổi một template thành một render function, các VNode tương ứng với nội dung tĩnh có thể được tạo ra chỉ một lần và được tái sử dụng. Những VNode tĩnh này được mô tả như là hoisted vì chúng được tạo ra bên ngoài render function trước khi nó chạy. Một dạng hoisting tương tự được áp dụng cho các object tĩnh hoặc mảng tĩnh được tạo ra bởi trình biên dịch template.

Để biết thêm chi tiết, xem:
- [Hướng dẫn - Cơ chế Rendering - Static Hoisting](/guide/extras/rendering-mechanism.html#static-hoisting)

## in-DOM template {#in-dom-template}

Có nhiều cách khác nhau để chỉ định một template cho một component. Trong hầu hết mọi trường hợp, template được cung cấp dưới dạng chuỗi.

Thuật ngữ *in-DOM template* được sử dụng để chỉ trường hợp mà template được cung cấp dưới dạng các DOM node thay vì chuỗi. Vue sau đó sẽ chuyển đổi các DOM node này thành một chuỗi template bằng cách sử dụng `innerHTML`.

Thông thường, in-DOM template thường được tạo ra bằng cách viết HTML trực tiếp vào HTML của trang. Sau đó, trình duyệt phân tích nó thành các DOM node mà Vue sử dụng để đọc `innerHTML`.

Để biết thêm chi tiết, xem:
- [Hướng dẫn - Khởi tạo một Application - In-DOM Root Component Template](/guide/essentials/application.html#in-dom-root-component-template)
- [Hướng dẫn - Cơ bản về Component - in-DOM Template Parsing Caveats](/guide/essentials/component-basics.html#in-dom-template-parsing-caveats)
- [Options: Rendering - template](/api/options-rendering.html#template)

## inject {#inject}

Xem [provide / inject](#provide-inject).

## lifecycle hooks {#lifecycle-hooks}

Một instance của Vue component sẽ phải trải qua một lifecycle. Ví dụ, một component sẽ được tạo ra, được mount, được update và được unmount.

Các *lifecycle hooks* là một cách để lắng nghe các lifecycle event này.

Với Options API, mỗi lifecycle hook được cung cấp như một option riêng biệt, ví dụ như `mounted`. Composition API sử dụng các function thay thế, ví dụ như `onMounted()`.

Để biết thêm chi tiết, xem:
- [Hướng dẫn - Lifecycle Hooks](/guide/essentials/lifecycle.html)

## macro {#macro}

Xem [compiler macro](#compiler-macro).

## named slot {#named-slot}

Một component có thể có nhiều slot, được phân biệt bởi name. Các slot khác với slot mặc định được gọi là *named slot*.

Để biết thêm chi tiết, xem:
- [Hướng dẫn - Slots - Named Slots](/guide/components/slots.html#named-slots)

## Options API {#options-api}

Vue được định nghĩa bằng các object. Các thuộc tính của các object này được gọi là *options*.

Các component có thể được viết theo hai kiểu khác nhau. Một kiểu sử dụng [Composition API](#composition-api) kết hợp với `setup` (hoặc thông qua option `setup()` hoặc `<script setup>`). Kiểu còn lại không sử dụng trực tiếp Composition API, thay vào đó sử dụng các option của component để đạt được kết quả tương tự. Các option được sử dụng theo cách này được gọi là *Options API*.

Options API bao gồm các option như `data()`, `computed`, `methods` và `created()`.

Một số option, như `props`, `emits` và `inheritAttrs`, có thể được sử dụng khi viết component theo cả hai kiểu. Vì chúng là các option của component, nên chúng có thể được coi là một phần của Options API. Tuy nhiên, vì các option này cũng được sử dụng kết hợp với `setup()`, nên nó thường hữu ích hơn khi xem chúng là những API được chia sẻ giữa hai kiểu component.

Bản thân hàm `setup()` cũng là một option của component, vì vậy nó *có thể* được coi là một phần của Options API. Tuy nhiên, đây không phải là cách mà thuật ngữ 'Options API' được sử dụng thông thường. Thay vào đó, hàm `setup()` được coi là một phần của Composition API.

## plugin {#plugin}

Mặc dù thuật ngữ *plugin* có thể được sử dụng trong nhiều ngữ cảnh khác nhau, Vue có một khái niệm cụ thể về plugin như là cách để thêm chức năng cho một ứng dụng.

Plugin được thêm vào một ứng dụng bằng cách gọi `app.use(plugin)`. Bản thân plugin là một function hoặc một object với một function `install`. Function này sẽ được truyền vào instance của ứng dụng và có thể làm bất cứ điều gì nó cần làm.

Để biết thêm chi tiết, xem:
- [Hướng dẫn - Plugins](/guide/reusability/plugins.html)

## prop {#prop}

Có 3 cách thông dụng để sử dụng thuật ngữ *prop* trong Vue:

* Component props
* VNode props
* Slot props

*Component props* là những gì phần lớn người dùng nghĩ đến khi nói đến props. Chúng được định nghĩa rõ ràng bởi component thông qua `defineProps()` hoặc option `props`.

Thuật ngữ *VNode props* được sử dụng để chỉ các thuộc tính của object được truyền vào như là tham số thứ hai của `h()`. Chúng có thể bao gồm component props, nhưng cũng có thể bao gồm các event của component, các event của DOM, các thuộc tính (attribute) của DOM và các đặc tính (property) của DOM. Bạn thường chỉ gặp VNode props khi làm việc với render function để trực tiếp thao tác các VNode.

*Slot props* là các thuộc tính được truyền vào một slot.

Trong tất cả mọi trường hợp, props là các thuộc tính được truyền vào từ bên ngoài.

Mặc dù từ props có nguồn gốc từ từ *properties*, nhưng thuật ngữ props có một ý nghĩa cụ thể hơn trong ngữ cảnh của Vue. Bạn nên tránh sử dụng nó như một cách viết tắt của properties.

Để biết thêm chi tiết, xem:
- [Hướng dẫn - Props](/guide/components/props.html)
- [Hướng dẫn - Render Functions & JSX](/guide/extras/render-function.html)
- [Hướng dẫn - Slots - Scoped Slots](/guide/components/slots.html#scoped-slots)

## provide / inject {#provide-inject}

`provide` và `inject` là một cách để truyền dữ liệu giữa các component.

Khi một component *provide* một giá trị, tất cả các component con của nó có thể chọn lấy giá trị đó, sử dụng `inject`. Khác với props, component cung cấp không biết chính xác component nào đang nhận giá trị.

`provide` và `inject` thường được sử dụng để tránh *prop drilling*. Chúng cũng có thể được sử dụng như một cách ngầm để một component giao tiếp với nội dung của slot.

`provide` cũng có thể được sử dụng ở application level, tạo ra cho một giá trị có sẵn cho tất cả các component trong ứng dụng đó.

Để biết thêm chi tiết, xem:
- [Hướng dẫn - provide / inject](/guide/components/provide-inject.html)

## reactive effect {#reactive-effect}

*Reactive effect* là một phần hệ thống reactivity của Vue. Nó liên quan đến việc theo dõi các dependency của một function và chạy lại function đó khi các giá trị của các dependency thay đổi.

`watchEffect()` là cách trực tiếp nhất để tạo ra một reactive effect. Các phần khác của Vue cũng sử dụng reactive effect bên trong. Ví dụ, việc render component, `computed()` và `watch()`.

Vue chỉ có thể theo dõi các reactive dependency bên trong một reactive effect. Nếu giá trị của một property được đọc bên ngoài một reactive effect, nó sẽ 'mất' đi tính reactive, việc này có nghĩa là Vue sẽ không biết phải làm gì nếu property đó thay đổi sau đó.

Thuật ngữ này bắt nguồn từ *side effect*. Việc gọi effect function là một side effect của việc thay đổi giá trị property.

Để biết thêm chi tiết, xem:
- [Hướng dẫn - Nền tảng về Reactivity](/guide/extras/reactivity-in-depth.html)

## reactivity {#reactivity}

Nhìn chung, *reactivity* được sử dụng để chỉ khả năng thực hiện các hành động tự động khi dữ liệu thay đổi. Ví dụ, cập nhật DOM hoặc thực hiện một network request khi một giá trị dữ liệu thay đổi.

Trong ngữ cảnh của Vue, reactivity được sử dụng để mô tả một tập hợp các tính năng. Các tính năng này kết hợp với nhau để tạo thành một *reactivity system*, được cung cấp thông qua [Reactivity API](#reactivity-api).

Có rất nhiều cách khác nhau để tạo ra một reactivity system. Ví dụ, nó có thể được thực hiện bằng cách phân tích cú pháp của code để xác định các dependency. Tuy nhiên, Vue không sử dụng hình thức reactivity system đó.

Thay vào đó, Vue sử dụng một hệ thống reactivity dựa trên việc theo dõi việc truy cập vào các property tại runtime. Nó thực hiện điều này bằng cách sử dụng các Proxy wrapper và các function getter/setter cho các property.

Để biết thêm chi tiết, xem:
- [Hướng dẫn - Nền tảng về Reactivity](/guide/essentials/reactivity-fundamentals.html)
- [Hướng dẫn - Chuyên sâu về Reactivity](/guide/extras/reactivity-in-depth.html)

## Reactivity API {#reactivity-api}

*Reactivity API* là một tập hợp các function liên quan đến [reactivity](#reactivity). Chúng có thể được sử dụng độc lập với các component. Nó bao gồm các function như `ref()`, `reactive()`, `computed()`, `watch()` và `watchEffect()`.

Reactivity API là một phần của Composition API.

Để biết thêm chi tiết, xem:
- [Reactivity API: Core](/api/reactivity-core.html)
- [Reactivity API: Utilities](/api/reactivity-utilities.html)
- [Reactivity API: Advanced](/api/reactivity-advanced.html)

## ref {#ref}

> Phần này nói về việc sử dụng `ref` để tạo ra reactivity. Để biết thêm về việc sử dụng `ref` trong template, xem [template ref](#template-ref).

`ref` là một phần của hệ thống Vue reactivity. Nó là một object với một property reactive duy nhất, được gọi là `value`.

Có rất nhiều cách khác nhau để tạo ra một ref. Ví dụ, `ref()` và `shallowRef()` có thể được sử dụng để tạo ra một ref. Function `isRef()` có thể được sử dụng để kiểm tra xem một object có phải là một ref hay không, và `isReadonly()` có thể được sử dụng để kiểm tra xem ref có cho phép gán lại giá trị của nó hay không.

Để biết thêm chi tiết, xem:
- [Hướng dẫn - Nền tảng về Reactivity](/guide/essentials/reactivity-fundamentals.html)
- [Reactivity API: Core](/api/reactivity-core.html)
- [Reactivity API: Utilities](/api/reactivity-utilities.html)
- [Reactivity API: Advanced](/api/reactivity-advanced.html)

## render function {#render-function}

*Render function* là một phần component, có nhiệm vụ tạo ra các VNode được sử dụng để render component đó. Các template được biên dịch thành một render function.

Để biết thêm chi tiết, xem:
- [Hướng dẫn - Render Functions & JSX](/guide/extras/render-function.html)

## scheduler {#scheduler}

*Scheduler* là một phần thuộc cấu trúc bên trong của Vue, điều khiển việc chạy các [reactive effect](#reactive-effect).

Khi reactive state thay đổi, Vue không ngay lập tức kích hoạt các cập nhật render. Thay vào đó, nó sẽ gom chúng lại và sử dụng một queue để chạy chúng. Điều này đảm bảo rằng một component chỉ được render lại một lần, ngay cả khi có nhiều thay đổi được thực hiện trên dữ liệu cơ sở.

[Watchers](/guide/essentials/watchers.html) cũng được gom lại và sử dụng cùng một queue với reactive effect. Watchers với `flush: 'pre'` (mặc định) sẽ chạy trước khi component được render, trong khi các watcher với `flush: 'post'` sẽ chạy sau khi component được render.

Các công việc trong scheduler cũng được sử dụng để thực hiện nhiều nhiệm vụ nội bộ khác nhau, chẳng hạn như kích hoạt một số [lifecycle hooks](#lifecycle-hooks) và cập nhật [template refs](#template-ref).

## scoped slot {#scoped-slot}

Thuật ngữ *scoped slot* được sử dụng để chỉ một [slot](#slot) nhận [props](#prop).

Trong quá khứ, Vue đã tạo ra một sự phân biệt lớn giữa scoped slot và non-scoped slot, tới mức mà chúng có thể được coi là hai tính năng riêng biệt, được thống nhất bởi một cú pháp template chung.

Trong Vue 3, các API của slot được đơn giản hóa để tất cả các slot đều hoạt động giống như scoped slot. Tuy nhiên, các use case cho scoped slot và non-scoped slot thường khác nhau, vì vậy thuật ngữ này vẫn hữu ích như một cách để chỉ các slot với props.

Prop được truyền vào một slot chỉ có thể được sử dụng trong một vùng cụ thể của template cha, có trách nhiệm định nghĩa nội dung của slot. Vùng của template này hoạt động như một biến phạm vi cho props, vì vậy được gọi là *scoped slot*.

Để biết thêm chi tiết, xem:
- [Hướng dẫn - Slots - Scoped Slots](/guide/components/slots.html#scoped-slots)

## SFC {#sfc}

Xem [Single-File Component](#single-file-component).

## side effect {#side-effect}

Thuật ngữ *side effect* không riêng biệt cho Vue. Nó được sử dụng để mô tả các hoạt động hoặc function làm điều gì đó ngoài phạm vi local của chúng.

Ví dụ, trong ngữ cảnh thiệt lập một property như `user.name = null`, điều được dự kiến là giá trị của `user.name` được thay đổi. Nếu nó cũng làm một điều gì đó khác, chẳng hạn như kích hoạt hệ thống reactivity của Vue, thì việc này sẽ được mô tả là một side effect. Đây là nguồn gốc của thuật ngữ [reactive effect](#reactive-effect) trong Vue.

Khi một function được mô tả là có side effect, việc này có nghĩa là function đó không chỉ trả về một giá trị, mà còn thực hiện một hành động nào đó. Ví dụ, function `console.log()` có side effect, vì nó in ra một giá trị ra console.

Thuật ngữ này thường được sử dụng khi mô tả việc rendering hoặc các thuộc tính computed. Rendering mà không có side effect được xem là best practice. Tương tự, function getter cho một computed property cũng nên không có side effect.

## Single-File Component {#single-file-component}

Thuật ngữ *Single-File Component*, hoặc SFC, được sử dụng để chỉ định dạng tệp `.vue`, được sử dụng phổ biến cho Vue component.

Xem thêm:
- [Hướng dẫn - Single-File Components](/guide/scaling-up/sfc.html)
- [SFC Syntax Specification](/api/sfc-spec.html)

## slot {#slot}

Slot được sử dụng để truyền nội dung vào một component con. Giống như prop được sử dụng để truyền dữ liệu, slot được sử dụng để truyền các nội dung HTML và các component Vue.

Để biết thêm chi tiết, xem:
- [Hướng dẫn - Slots](/guide/components/slots.html)

## template ref {#template-ref}

Thuật ngữ *template ref* được sử dụng để chỉ việc sử dụng thuộc tính `ref` trên một thẻ bên trong một template. Sau khi component được render, thuộc tính này được sử dụng để điền vào một property tương ứng với phần tử HTML hoặc instance component tương ứng với thẻ trong template.

Nếu bạn đang sử dụng Options API, thì các refs được expose thông qua các property của object `$refs`.

Với Composition API, template refs tạo ra một [ref](#ref) reactive với cùng tên.

Template refs không nên bị nhầm lẫn với reactive refs được tìm thấy trong hệ thống reactivity của Vue.

Để biết thêm chi tiết, xem:
- [Hướng dẫn - Template Refs](/guide/essentials/template-refs.html)

## VDOM {#vdom}

Xem [virtual DOM](#virtual-dom).

## virtual DOM {#virtual-dom}

Thuật ngữ *virtual DOM* (VDOM) không chỉ riêng biệt cho Vue. Đây là một phương pháp chung được sử dụng bởi nhiều framework web để quản lý việc cập nhật UI.

Các trình duyệt sử dụng một cây gồm các node để biểu diễn trạng thái hiện tại của trang. Cây đó, và các API JavaScript được sử dụng để tương tác với nó, được gọi là *document object model*, hoặc *DOM*.

Thao túng DOM là một điểm chính của hiệu năng. Virtual DOM cung cấp một chiến lược để quản lý điểm này.

Thay vì tạo ra các node DOM trực tiếp, Vue component tạo ra một mô tả về các node DOM mà chúng muốn. Mô tả này được biết đến như là các *virtual DOM node*, hoặc *VNode*. Tạo ra các VNode là một quá trình tương đối rẻ.

Mỗi khi một component được render lại, một cây VNode mới được tạo ra. Cây VNode mới này được so sánh với cây VNode cũ, và bất kỳ sự khác biệt nào giữa hai cây VNode này sau đó sẽ được áp dụng vào DOM thực. Nếu không có gì thay đổi, thì DOM không cần được thay đổi.

Vue sử dụng một cách tiếp cận hỗn hợp gọi là [Compiler-Informed Virtual DOM](/guide/extras/rendering-mechanism.html#compiler-informed-virtual-dom). Trình biên dịch template của Vue có thể áp dụng các tối ưu hiệu suất dựa trên phân tích tĩnh của template. Thay vì thực hiện một so sánh đầy đủ giữa cây VNode cũ và mới của một component tại runtime, Vue có thể sử dụng thông tin được trích xuất bởi trình biên dịch để giảm thiểu việc so sánh chỉ với các phần của cây có thể thay đổi thực sự.

Để biết thêm chi tiết, xem:
- [Hướng dẫn  - Cơ chế Rendering](/guide/extras/rendering-mechanism.html)
- [Hướng dẫn  - Render Functions & JSX](/guide/extras/render-function.html)

## VNode {#vnode}

Một *VNode* là một *DOM node ảo*. Chúng có thể được tạo ra bằng cách sử dụng function [`h()`](/api/render-function.html#h).

Xem [virtual DOM](#virtual-dom) để biết thêm chi tiết.

## Web Component {#web-component}

Tiêu chuẩn *Web Components* là một tập hợp các tính năng được triển khai trong các trình duyệt web hiện đại.

Vue component không phải là Web Components. Tuy nhiên, `defineCustomElement()` có thể được sử dụng để tạo ra một [custom element](#custom-element) từ một Vue component. Vue cũng hỗ trợ việc sử dụng các custom element bên trong các Vue component.

Để biết thêm chi tiết, xem:
- [Hướng dẫn - Vue và Web Components](/guide/extras/web-components.html)
