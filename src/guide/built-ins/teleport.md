# Teleport {#teleport}

 <VueSchoolLink href="https://vueschool.io/lessons/vue-3-teleport" title="Free Vue.js Teleport Lesson"/>

`<Teleport>` là một component tích hợp sẵn cho phép chúng ta "**dịch chuyển**" một phần của template của component vào một DOM node tồn tại bên ngoài cây DOM của component đó.

## Ứng dụng cơ bản {#basic-usage}

Đôi khi chúng ta có thể gặp phải tình huống sau: một phần của template của component vốn thuộc chính nó, nhưng từ mặt trực quan, nó nên được hiển thị ở một nơi khác trong DOM, bên ngoài ứng dụng Vue.

Ví dụ phổ biến nhất là khi xây dựng một modal full-screen. Lý tưởng nhất, chúng ta muốn button của modal và modal chính nó nằm trong cùng một component, vì chúng đều liên quan đến trạng thái mở / đóng của modal. Nhưng điều đó có nghĩa là modal sẽ được render cùng với button, nằm sâu trong cây DOM của ứng dụng. Điều này có thể tạo ra một số vấn đề khó khăn khi định vị modal thông qua CSS.

Hãy xem xét cấu trúc HTML sau.

```vue-html
<div class="outer">
  <h3>Ví dụ về Vue Teleport</h3>
  <div>
    <MyModal />
  </div>
</div>
```

Và đây là cấu trúc của `<MyModal>`:

<div class="composition-api">

```vue
<script setup>
import { ref } from 'vue'

const open = ref(false)
</script>

<template>
  <button @click="open = true">Mở Modal</button>

  <div v-if="open" class="modal">
    <p>Xin chào, đây là modal!</p>
    <button @click="open = false">Đóng</button>
  </div>
</template>

<style scoped>
.modal {
  position: fixed;
  z-index: 999;
  top: 20%;
  left: 50%;
  width: 300px;
  margin-left: -150px;
}
</style>
```

</div>
<div class="options-api">

```vue
<script>
export default {
  data() {
    return {
      open: false
    }
  }
}
</script>

<template>
  <button @click="open = true">Mở Modal</button>

  <div v-if="open" class="modal">
    <p>Xin chào, đây là modal!</p>
    <button @click="open = false">Đóng</button>
  </div>
</template>

<style scoped>
.modal {
  position: fixed;
  z-index: 999;
  top: 20%;
  left: 50%;
  width: 300px;
  margin-left: -150px;
}
</style>
```

</div>

Component chứa một `<button>` để kích hoạt mở modal, và một `<div>` với class `.modal`, chứa nội dung của modal và một button để đóng modal.

Khi sử dụng component này trong cấu trúc HTML ban đầu, có một số vấn đề tiềm ẩn:

- `position: fixed` chỉ định vị trí của element dựa trên viewport khi không có ancestor element nào có thuộc tính `transform`, `perspective` hoặc `filter` được set. Nếu, ví dụ, chúng ta có ý định animate ancestor `<div class="outer">` với một CSS transform, nó sẽ làm hỏng layout của modal!

- Thuộc tính `z-index` của model được giới hạn bởi các element chứa nó. Nếu có một element khác mà trùng với `<div class="outer">` và có `z-index` cao hơn, nó sẽ che phủ modal của chúng ta.

`<Teleport>` cung cấp một cách ổn định để giải quyết những vấn đề này, cho phép chúng ta thoát khỏi cấu trúc DOM lồng nhau. Hãy sửa đổi `<MyModal>` để sử dụng `<Teleport>`:

```vue-html{3,8}
<button @click="open = true">Mở Modal</button>

<Teleport to="body">
  <div v-if="open" class="modal">
    <p>Xin chào, đây là modal!</p>
    <button @click="open = false">Đóng</button>
  </div>
</Teleport>
```

Thuộc tính `to` chỉ định mục tiêu của `<Teleport>` sẽ nhận một chuỗi CSS selector hoặc một DOM node. Ở đây, chúng ta đang nói với Vue rằng "**dịch chuyển**" nội dung của `<Teleport>` vào `<body>`.

Bạn có thể click vào button bên dưới và kiểm tra `<body>` tag trong devtools của trình duyệt:

<script setup>
import { ref } from 'vue'
const open = ref(false)
</script>

<div class="demo">
  <button @click="open = true">Mở Modal</button>
  <ClientOnly>
    <Teleport to="body">
      <div v-if="open" class="demo modal-demo">
        <p style="margin-bottom:20px">Xin chào, đây là modal!</p>
        <button @click="open = false">Đóng</button>
      </div>
    </Teleport>
  </ClientOnly>
</div>

<style>
.modal-demo {
  position: fixed;
  z-index: 999;
  top: 20%;
  left: 50%;
  width: 300px;
  margin-left: -150px;
  background-color: var(--vt-c-bg);
  padding: 30px;
  border-radius: 8px;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.15);
}
</style>

Bạn có thể kết hợp `<Teleport>` với [`<Transition>`](./transition) để tạo ra modal có hiệu ứng - xem [Ví dụ](/examples/#modal).

:::tip
Mục tiêu được khai báo trong thuộc tính `to` phải tồn tại trong DOM khi component `<Teleport>` được mounted. Lý tưởng nhất đó là một element nằm bên ngoài toàn bộ ứng dụng Vue. Nếu muốn dịch chuyển đến một element khác được render bởi Vue, bạn cần đảm bảo element đó đã được mounted trước khi `<Teleport>` được mounted.
:::

## Sử dụng với component {#using-with-components}

`<Teleport>` chỉ chỉnh sửa cấu trúc DOM đã được render, nó không ảnh hưởng đến cấu trúc component. Nói cách khác, nếu `<Teleport>` chứa một component, component đó vẫn là con trực tiếp của component chứa `<Teleport>`. Props và event vẫn hoạt động như bình thường.

Điều này cũng có nghĩa là inject từ component cha vẫn hoạt động bình thường, và component con sẽ được lồng vào dưới component cha trong Vue Devtools, thay vì được đặt vào nơi nội dung thực sự được dịch chuyển đến.

## Tắt Teleport {#disabling-teleport}

Trong một vài trường hợp chúng ta có thể muốn tắt `<Teleport>` theo điều kiện. Ví dụ, chúng ta có thể muốn render một component như một overlay cho desktop, nhưng inline cho mobile. `<Teleport>` hỗ trợ thuộc tính `disabled` có thể được toggle một cách dynamic:

```vue-html
<Teleport :disabled="isMobile">
  ...
</Teleport>
```

State `isMobile` có thể được cập nhật dynamic bằng cách kiểm tra sự thay đổi của media query.

## Nhiều Teleport trên cùng một mục tiêu {#multiple-teleports-on-the-same-target}

Một use case phổ biến đó là một component `<Modal>` có thể được sử dụng lại, với khả năng có nhiều instance hoạt động cùng một lúc. Với loại scenario này, nhiều `<Teleport>` có thể mount nội dung của chúng vào cùng một element mục tiêu. Thứ tự của các modal sẽ theo như phương thức append - các mount sau sẽ được đặt sau các mount trước đó trong element mục tiêu.

Ví dụ, chúng ta có thể có một component `<Modal>` như sau:

```vue-html
<Teleport to="#modals">
  <div>A</div>
</Teleport>
<Teleport to="#modals">
  <div>B</div>
</Teleport>
```

The rendered result would be:

```html
<div id="modals">
  <div>A</div>
  <div>B</div>
</div>
```

---

**Xem thêm**

- [Tham khảo `<Teleport>` API](/api/built-in-components#teleport)
- [Xử lý Teleport trong SSR](/guide/scaling-up/ssr#teleports)
