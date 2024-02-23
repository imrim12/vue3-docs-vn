# Single-File Components {#single-file-components}

## Giới thiệu {#introduction}

Vue Single-File Components (hay còn gọi là `*.vue` files, viết tắt là **SFC**) là một định dạng tệp đặc biệt cho phép chúng ta đóng gói các template, logic, **và** kiểu dáng của một thành phần Vue trong một tệp duy nhất. Dưới đây là một ví dụ về SFC:

<div class="options-api">

```vue
<script>
export default {
  data() {
    return {
      greeting: 'Hello World!'
    }
  }
}
</script>

<template>
  <p class="greeting">{{ greeting }}</p>
</template>

<style>
.greeting {
  color: red;
  font-weight: bold;
}
</style>
```

</div>

<div class="composition-api">

```vue
<script setup>
import { ref } from 'vue'
const greeting = ref('Hello World!')
</script>

<template>
  <p class="greeting">{{ greeting }}</p>
</template>

<style>
.greeting {
  color: red;
  font-weight: bold;
}
</style>
```

</div>

Như chúng ta có thể thấy, SFC Vue là một phần mở rộng tự nhiên của bộ ba cổ điển của HTML, CSS và JavaScript. Các khối `<template>`, `<script>` và `<style>` đóng gói và đặt cùng với nhau các chế độ xem, logic và kiểu dáng của một thành phần trong cùng một tệp. Cú pháp đầy đủ được định nghĩa trong [SFC Syntax Specification](/api/sfc-spec).

## Tại sao lại là SFC {#why-sfc}

Mặc dù SFCs yêu cầu một bước xây dựng, nhưng lại có nhiều lợi ích:

- Người dùng có thể mô-đun hóa các thành phần bằng cách sử dụng cú pháp HTML, CSS và JavaScript quen thuộc
- [Tập trung các vấn đề mật thiết cùng vị trí.](#what-about-separation-of-concerns)
- Biên dịch trước các template mà không có chi phí biên dịch thời gian chạy
- [CSS trong phạm vi component](/api/sfc-css-features)]
- [Cú pháp linh hoạt hơn khi làm việc với Composition API.](/api/sfc-script-setup)
- Tối ưu hóa thời gian biên dịch bằng cách phân tích chéo các template và script
- [Hỗ trợ IDE](/guide/scaling-up/tooling#ide-support) với tự động hoàn thành và kiểm tra kiểu cho các biểu thức template
- Hỗ trợ thay mô-đun tại chỗ (Hot-Module Replacement - HMR)

SFC là một đặc điểm xác định của Vue như một framework, và là phương pháp được khuyến khích để sử dụng Vue trong các kịch bản sau:

- Ứng dụng đơn trang (Single-Page Applications - SPA)
- Ứng dụng tạo trang tĩnh (Static Site Generation - SSG)
- Bất kỳ ứng dụng frontend phức tạp nào cần bước build để tối ưu trải nghiệm phát triển (DX). 

Nói như vậy, chúng tôi nhận thức rằng có các kịch bản mà SFC có thể cảm thấy quá mức. Đây là lý do tại sao Vue vẫn có thể được sử dụng thông qua JavaScript thuần mà không cần bước xây dựng. Nếu bạn chỉ đang tìm cách tăng cường HTML tĩnh với các tương tác nhẹ, bạn cũng có thể kiểm tra [petite-vue](https://github.com/vuejs/petite-vue), một tập hợp con 6 kB của Vue được tối ưu hóa cho việc tăng cường tiến bộ.

## Cách hoạt động {#how-it-works}

Vue SFC là một định dạng tệp đặc biệt và phải được biên dịch trước bằng [@vue/compiler-sfc](https://github.com/vuejs/core/tree/main/packages/compiler-sfc) thành JavaScript và CSS chuẩn. Một SFC đã được biên dịch là một module JavaScript chuẩn (ES) - điều này có nghĩa là với thiết lập xây dựng đúng đắn, bạn có thể nhập một SFC như một module:

```js
import MyComponent from './MyComponent.vue'

export default {
  components: {
    MyComponent
  }
}
```

Thẻ tag `<style>` bên trong SFC thường được chèn như là các thẻ `<style>` native trong quá trình phát triển để hỗ trợ hot update. Đối với production, chúng có thể được trích xuất và hợp nhất thành một tệp CSS duy nhất.

Bạn có thể chơi với SFC và khám phá cách chúng được biên dịch trong [Vue SFC Playground](https://play.vuejs.org/).

Trong các dự án thực tế, chúng tôi thường tích hợp trình biên dịch SFC với một công cụ xây dựng như [Vite](https://vitejs.dev/) hoặc [Vue CLI](http://cli.vuejs.org/) (dựa trên [webpack](https://webpack.js.org/)), và Vue cung cấp các công cụ xây dựng mẫu chính thức để giúp bạn bắt đầu với SFC càng nhanh càng tốt. Xem thêm chi tiết trong phần [SFC Tooling](/guide/scaling-up/tooling).

## Vậy còn việc Phân tách vấn đề thì sao ? {#what-about-separation-of-concerns}

Một vài người dùng đến từ một nền tảng phát triển web truyền thống có thể có mối quan tâm rằng SFC đang kết hợp các vấn đề khác nhau trong cùng một nơi - trong khi HTML/CSS/JS nên được để tách biệt!

Để trả lời câu hỏi này, điều quan trọng là chúng ta phải đồng ý rằng **phân tách vấn đề không tương đương với phân tách các loại tệp**. Mục tiêu cuối cùng của các nguyên tắc kỹ thuật là cải thiện khả năng bảo trì của các codebase. Phân tách vấn đề, khi được áp dụng một cách độc đoán như phân tách các loại tệp, không giúp chúng ta đạt được mục tiêu đó trong bối cảnh các ứng dụng frontend ngày càng phức tạp hơn.

Trong việc phát triển UI hiện đại, chúng tôi đã thấy rằng thay vì chia codebase thành ba lớp lớn mà xen kẽ với nhau, nó có ý nghĩa hơn nhiều khi chia chúng thành các thành phần lỏng lẻo và kết hợp chúng lại với nhau. Bên trong một thành phần, template, logic và kiểu dáng của nó được kết hợp một cách bẩm sinh, và đặt chúng cùng nhau thực sự làm cho thành phần trở nên gắn kết hơn và dễ bảo trì hơn.

Lưu ý rằng kể cả nếu bạn không thích ý tưởng về Single-File Components, bạn vẫn có thể tận dụng các tính năng hot-reloading và biên dịch trước của nó bằng cách tách riêng JavaScript và CSS của bạn thành các tệp riêng biệt sử dụng [Src Imports](/api/sfc-spec#src-imports).
