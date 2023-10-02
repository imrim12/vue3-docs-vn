# Lifecycle Hooks {#lifecycle-hooks}

Mỗi thực thể Vue component trải qua một loạt các bước khởi tạo khi nó được tạo ra - ví dụ, nó cần thiết lập quan sát dữ liệu, biên dịch template, gắn thực thể vào DOM, và cập nhật DOM khi dữ liệu thay đổi. Trong quá trình đó, nó cũng chạy các hàm được gọi là lifecycle hooks, cho phép người dùng có thể thêm code vào từng giai đoạn cụ thể.

## Đăng ký Lifecycle Hooks {#registering-lifecycle-hooks}

Ví dụ, hook <span class="composition-api">`onMounted`</span><span class="options-api">`mounted`</span> có thể được sử dụng để chạy code sau khi component đã hoàn thành việc render và tạo các node DOM ban đầu:

<div class="composition-api">

```vue
<script setup>
import { onMounted } from 'vue'

onMounted(() => {
  console.log(`the component is now mounted.`)
})
</script>
```

</div>
<div class="options-api">

```js
export default {
  mounted() {
    console.log(`the component is now mounted.`)
  }
}
```

</div>

Cũng có những hook khác sẽ được gọi ở những giai đoạn khác nhau trong vòng đời của một thực thể, các hook được sử dụng phổ biến nhất là <span class="composition-api">[`onMounted`](/api/composition-api-lifecycle#onmounted), [`onUpdated`](/api/composition-api-lifecycle#onupdated), và [`onUnmounted`](/api/composition-api-lifecycle#onunmounted).</span><span class="options-api">[`mounted`](/api/options-lifecycle#mounted), [`updated`](/api/options-lifecycle#updated), và [`unmounted`](/api/options-lifecycle#unmounted).</span>

<div class="options-api">

Tất cả các lifecycle hooks đều được gọi với ngữ cảnh `this`, nó trỏ tới thực thể hiện tại đang gọi nó. Lưu ý điều này có nghĩa là bạn nên tránh sử dụng arrow function khi khai báo lifecycle hooks, vì nếu làm như vậy bạn sẽ không thể truy cập vào thực thể component thông qua `this`.

</div>

<div class="composition-api">

Khi gọi `onMounted`, Vue tự động liên kết callback function đã đăng ký với thực thể component hiện tại. Điều này yêu cầu các hook cần được đăng ký **đồng bộ** trong quá trình thiết lập component. Ví dụ, không làm như sau:

```js
setTimeout(() => {
  onMounted(() => {
    // this won't work.
  })
}, 100)
```

Lưu ý rằng điều này không có nghĩa là việc gọi hàm phải được đặt theo thứ tự bên trong `setup()` hoặc `<script setup>`. `onMounted()` có thể được gọi trong một hàm bên ngoài miễn là call stack đồng bộ và bắt nguồn bên trong `setup()`.

</div>

## Sơ đồ Lifecycle {#lifecycle-diagram}

Dưới đây là sơ đồ vòng đời của một thực thể. Bạn không cần phải hiểu toàn bộ mọi thứ bây giờ, nhưng khi bạn học và xây dựng nhiều hơn, nó sẽ là một tài liệu tham khảo hữu ích.

![Component lifecycle diagram](./images/lifecycle.png)

<!-- https://www.figma.com/file/Xw3UeNMOralY6NV7gSjWdS/Vue-Lifecycle -->

Xem <span class="composition-api">[Lifecycle Hooks API reference](/api/composition-api-lifecycle)</span><span class="options-api">[Lifecycle Hooks API reference](/api/options-lifecycle)</span> để biết chi tiết về tất cả các lifecycle hooks và các trường hợp sử dụng tương ứng của chúng.
