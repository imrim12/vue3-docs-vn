---
outline: deep
---

# Sử dụng Vue với TypeScript {#using-vue-with-typescript}

Một hệ thống kiểu giống như TypeScript có thể phát hiện được nhiều lỗi thông thường thông qua phân tích tĩnh tại thời gian biên dịch. Điều này giảm thiểu khả năng xảy ra lỗi trong runtime trên môi trường production, và cũng cho phép chúng ta tự tin hơn khi tái cấu trúc mã trong các ứng dụng quy mô lớn. TypeScript cũng cải thiện tính thẩm mỹ của nhà phát triển thông qua tự động hoàn thành dựa trên kiểu trong các IDE.

Vue được viết bằng TypeScript và cung cấp hỗ trợ TypeScript cấp đầu tiên. Tất cả các gói chính thức của Vue đều đi kèm với các khai báo kiểu được đóng gói sẵn, chúng nên hoạt động ngay khi sử dụng.

## Thiết lập dự án {#project-setup}

[`create-vue`](https://github.com/vuejs/create-vue), công cụ tạo dự án chính thức của Vue, cung cấp các tùy chọn để tạo dự án Vue được cấu hình sẵn với TypeScript.

### Tổng quan {#overview}

Với thiết lập dựa trên Vite, dev server và bundler chỉ thực hiện việc biên dịch và không thực hiện bất kỳ kiểm tra kiểu nào. Điều này đảm bảo rằng dev server Vite vẫn nhanh chóng ngay cả khi sử dụng TypeScript.

- Trong quá trình phát triển, chúng tôi khuyến khích dựa vào một [cài đặt IDE tốt](#ide-support) để có phản hồi tức thì về các lỗi kiểu.

- Nếu sử dụng SFC, hãy sử dụng tiện ích [`vue-tsc`](https://github.com/vuejs/language-tools/tree/master/packages/vue-tsc) để kiểm tra kiểu dòng lệnh và tạo ra các khai báo kiểu. `vue-tsc` là một bao bọc xung quanh `tsc`, giao diện dòng lệnh riêng của TypeScript. Nó hoạt động phần lớn giống như `tsc` ngoại trừ việc nó hỗ trợ SFC Vue ngoài các tệp TypeScript. Bạn có thể chạy `vue-tsc` trong chế độ theo dõi song song với Vite dev server, hoặc sử dụng một plugin Vite như [vite-plugin-checker](https://vite-plugin-checker.netlify.app/) mà chạy các kiểm tra trong một luồng làm việc riêng.

- Vue CLI cũng cung cấp hỗ trợ TypeScript, nhưng không còn được khuyến khích. Xem [ghi chú bên dưới](#note-on-vue-cli-and-ts-loader).

### Hỗ trợ IDE {#ide-support}

- [Visual Studio Code](https://code.visualstudio.com/) (VSCode) được khuyến khích sử dụng vì hỗ trợ tốt cho TypeScript ngay khi cài đặt.

  - [Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar) là tiện ích VSCode chính thức cung cấp hỗ trợ TypeScript bên trong SFC Vue, cùng với nhiều tính năng tuyệt vời khác.

    :::tip
    Volar thay thế [Vetur](https://marketplace.visualstudio.com/items?itemName=octref.vetur), tiện ích VSCode chính thức trước đây của chúng tôi cho Vue 2. Nếu bạn đã cài đặt Vetur, hãy chắc chắn tắt nó trong các dự án Vue 3.
    :::

  - [TypeScript Vue Plugin](https://marketplace.visualstudio.com/items?itemName=Vue.vscode-typescript-vue-plugin) cũng cần thiết để có được hỗ trợ kiểu cho các import `*.vue` trong các tệp TS.

- [WebStorm](https://www.jetbrains.com/webstorm/) cũng cung cấp hỗ trợ tích hợp cho cả TypeScript và Vue. Các IDE JetBrains khác cũng hỗ trợ chúng, hoặc thông qua [plugin miễn phí](https://plugins.jetbrains.com/plugin/9442-vue-js). Kể từ phiên bản 2023.2, WebStorm và Vue Plugin đi kèm với hỗ trợ tích hợp cho Vue Language Server. Bạn có thể đặt dịch vụ Vue để sử dụng tích hợp Volar trên tất cả các phiên bản TypeScript, trong Cài đặt > Ngôn ngữ & Frameworks > TypeScript > Vue. Theo mặc định, Volar sẽ được sử dụng cho các phiên bản TypeScript 5.0 và cao hơn.

### Cấu hình `tsconfig.json` {#configuring-tsconfig-json}

Dự án được khởi tạo thông qua `create-vue` bao gồm `tsconfig.json` được cấu hình sẵn. Cấu hình cơ bản được trừu tượng hóa trong gói [`@vue/tsconfig`](https://github.com/vuejs/tsconfig). Bên trong dự án, chúng tôi sử dụng [Project References](https://www.typescriptlang.org/docs/handbook/project-references.html) để đảm bảo các kiểu chính xác cho mã chạy trong các môi trường khác nhau (ví dụ: mã ứng dụng và mã kiểm tra nên có các biến toàn cục khác nhau).

Khi cấu hình `tsconfig.json` thủ công, một số tùy chọn đáng chú ý bao gồm:

- [`compilerOptions.isolatedModules`](https://www.typescriptlang.org/tsconfig#isolatedModules) được đặt thành `true` vì Vite sử dụng [esbuild](https://esbuild.github.io/) để biên dịch TypeScript và phụ thuộc vào các giới hạn biên dịch tệp đơn.

- Nếu bạn đang sử dụng Options API, bạn cần đặt [`compilerOptions.strict`](https://www.typescriptlang.org/tsconfig#strict) thành `true` (hoặc ít nhất bật [`compilerOptions.noImplicitThis`](https://www.typescriptlang.org/tsconfig#noImplicitThis), một phần của cờ `strict`) để tận dụng kiểm tra kiểu của `this` trong các tùy chọn component. Nếu không, `this` sẽ được coi là `any`.

- Nếu bạn đã cấu hình các bí danh giải quyết trong công cụ biên dịch của mình, ví dụ như bí danh `@/*` được cấu hình mặc định trong dự án `create-vue`, bạn cũng cần cấu hình nó cho TypeScript thông qua [`compilerOptions.paths`](https://www.typescriptlang.org/tsconfig#paths).

Xem thêm:

- [Tài liệu chính thức về tùy chọn biên dịch TypeScript](https://www.typescriptlang.org/docs/handbook/compiler-options.html)
- [Các lưu ý về biên dịch TypeScript của esbuild](https://esbuild.github.io/content-types/#typescript-caveats)

### Chế độ Takeover của Volar {#volar-takeover-mode}

> Phần này chỉ áp dụng cho VSCode + Volar.

Để có thể khiến cho Vue SFC và TypeScript hoạt động cùng nhau, Volar tạo một phiên dịch ngôn ngữ TS riêng biệt được vá với hỗ trợ cụ thể cho Vue, và sử dụng nó trong Vue SFC. Đồng thời, các tệp TS thuần vẫn được xử lý bởi dịch vụ ngôn ngữ TS tích hợp sẵn của VSCode, đó là lý do tại sao chúng ta cần [TypeScript Vue Plugin](https://marketplace.visualstudio.com/items?itemName=Vue.vscode-typescript-vue-plugin) để hỗ trợ các import Vue SFC trong các tệp TS. Thiết lập mặc định này hoạt động, nhưng đối với mỗi dự án, chúng ta đang chạy hai phiên dịch ngôn ngữ TS: một từ Volar, một từ dịch vụ ngôn ngữ TS tích hợp sẵn của VSCode. Điều này hơi không hiệu quả và có thể dẫn đến vấn đề hiệu suất trong các dự án lớn.

Volar cung cấp một tính năng gọi là "Chế độ Takeover" để cải thiện hiệu suất. Trong chế độ takeover, Volar cung cấp hỗ trợ cho cả tệp Vue và TS bằng một phiên dịch ngôn ngữ TS duy nhất.

Để kích hoạt chế độ Takeover, bạn cần tắt dịch vụ ngôn ngữ TS tích hợp sẵn của VSCode trong **dự án của bạn** bằng cách làm theo các bước sau:

1. Trong không gian làm việc dự án của bạn, mở bảng điều khiển lệnh với `Ctrl + Shift + P` (macOS: `Cmd + Shift + P`).
2. Gõ `built` và chọn "Extensions: Show Built-in Extensions".
3. Gõ `typescript` vào ô tìm kiếm tiện ích (đừng xóa tiền tố `@builtin`).
4. Nhấp vào biểu tượng bánh răng nhỏ của "TypeScript and JavaScript Language Features", và chọn "Disable (Workspace)".
5. Tải lại không gian làm việc. Chế độ Takeover sẽ được kích hoạt khi bạn mở một tệp Vue hoặc TS.

<img src="./images/takeover-mode.png" width="590" height="426" style="margin:0px auto;border-radius:8px">

### Lưu ý cho Vue CLI và `ts-loader` {#note-on-vue-cli-and-ts-loader}

Trong thiết lập dựa trên webpack như Vue CLI, việc thực hiện kiểm tra kiểu là một phần của luồng biến đổi module, ví dụ như với `ts-loader`. Tuy nhiên, đây không phải là một giải pháp sạch sẽ vì hệ thống kiểu cần biết về toàn bộ đồ thị module để thực hiện kiểm tra kiểu. Bước biến đổi của mỗi module đơn giản không phải là nơi thích hợp để thực hiện kiểm tra kiểu. Điều này dẫn đến các vấn đề sau:

- `ts-loader` chỉ có thể kiểm tra kiểu mã sau khi biến đổi. Điều này không phù hợp với các lỗi mà chúng ta thấy trong IDE hoặc từ `vue-tsc`, mà ánh xạ trực tiếp trở lại mã nguồn.

- Kiểm tra kiểu có thể chậm. Khi nó được thực hiện trong cùng một luồng / quá trình với các biến đổi mã, nó ảnh hưởng đáng kể đến tốc độ biên dịch của toàn bộ ứng dụng.

- Chúng ta đã có sẵn kiểm tra kiểu chạy ngay trong IDE, trong một quá trình riêng biệt, vì vậy việc làm chậm trải nghiệm phát triển không phải là một sự đánh đổi tốt.

Nếu bạn hiện đang sử dụng Vue 3 + TypeScript thông qua Vue CLI, chúng tôi khuyến nghị chuyển sang Vite. Chúng tôi cũng đang làm việc trên các tùy chọn CLI để kích hoạt hỗ trợ TS chỉ biên dịch, để bạn có thể chuyển sang `vue-tsc` cho kiểm tra kiểu.

## Lưu ý sử dụng chung {#general-usage-notes}

### `defineComponent()` {#definecomponent}

Để cho TypeScript có thể suy luận kiểu một cách chính xác bên trong các tùy chọn component, chúng ta cần định nghĩa các component với [`defineComponent()`](/api/general#definecomponent):

```ts
import { defineComponent } from 'vue'

export default defineComponent({
  // kích hoạt suy luận kiểu
  // ...
  props: {
    name: String,
    msg: { type: String, required: true }
  },
  data() {
    return {
      count: 1
    }
  },
  mounted() {
    this.name // kiểu dữ liệu: string | undefined
    this.msg // kiểu dữ liệu: string
    this.count // kiểu dữ liệu: number
  }
})
```

`defineComponent()` cũng hỗ trợ suy luận kiểu cho props được truyền vào `setup()` khi sử dụng Composition API mà không cần `<script setup>`:

```ts
import { defineComponent } from 'vue'

export default defineComponent({
  // kích hoạt suy luận kiểu
  props: {
    message: String
  },
  setup(props) {
    props.message // kiểu dữ liệu: string | undefined
  }
})
```

Xem thêm:

- [Lưu ý cho Treeshaking trong webpack](/api/general#note-on-webpack-treeshaking)
- [Kiểm tra kiểu cho `defineComponent`](https://github.com/vuejs/core/blob/main/packages/dts-test/defineComponent.test-d.tsx)

:::tip
`defineComponent()` cũng kích hoạt suy luận kiểu cho các component được định nghĩa trong JavaScript thuần.
:::

### Sử dụng trong Single-File Components {#usage-in-single-file-components}

Để sử dụng TypeScript trong SFCs, thêm thuộc tính `lang="ts"` vào các thẻ `<script>`. Khi `lang="ts"` được sử dụng, tất cả các biểu thức mẫu cũng được hưởng kiểm tra kiểu nghiêm ngặt hơn.

```vue
<script lang="ts">
import { defineComponent } from 'vue'

export default defineComponent({
  data() {
    return {
      count: 1
    }
  }
})
</script>

<template>
  <!-- kích hoạt kiểm tra kiểu và tự động hoàn thành -->
  {{ count.toFixed(2) }}
</template>
```

`lang="ts"` cũng có thể được sử dụng với `<script setup>`:

```vue
<script setup lang="ts">
// Kích hoạt TypeScript
import { ref } from 'vue'

const count = ref(1)
</script>

<template>
  <!-- kích hoạt kiểm tra kiểu và tự động hoàn thành -->
  {{ count.toFixed(2) }}
</template>
```

### TypeScript trong Templates {#typescript-in-templates}

`<template>` cũng hỗ trợ TypeScript trong các biểu thức ràng buộc khi sử dụng `<script lang="ts">` hoặc `<script setup lang="ts">`. Điều này hữu ích trong các trường hợp bạn cần thực hiện ép kiểu trong biểu thức mẫu.

Dưới đây là một ví dụ được tạo ra một cách cố ý:

```vue
<script setup lang="ts">
let x: string | number = 1
</script>

<template>
  <!-- lỗi vì x có thể là kiểu chuỗi -->
  {{ x.toFixed(2) }}
</template>
```

Điều này có thể được giải quyết với một ép kiểu trực tiếp:

```vue{6}
<script setup lang="ts">
let x: string | number = 1
</script>

<template>
  {{ (x as number).toFixed(2) }}
</template>
```

:::tip
Nếu bạn đang sử dụng Vue CLI hoặc một thiết lập dựa trên webpack, TypeScript trong biểu thức mẫu yêu cầu `vue-loader@^16.8.0`.
:::

### Sử dụng với TSX

Vue cũng hỗ trợ viết component bằng JSX / TSX. Chi tiết được đề cập trong hướng dẫn [Render Function & JSX](/guide/extras/render-function.html#jsx-tsx).

## Generic Components {#generic-components}

Generic component được hỗ trợ trong hai trường hợp:

- Trong SFCs: [`<script setup>` với thuộc tính `generic`](/api/sfc-script-setup.html#generics)
- Render function / JSX component: [`defineComponent()`'s function signature](/api/general.html#function-signature)

## Các công thức dành cho API {#api-specific-recipes}

- [TS với Composition API](./composition-api)
- [TS với Options API](./options-api)
