---
outline: deep
---

# Hướng dẫn về phong cách viết {#style-guide}

:::warning Lưu ý trạng thái của bài viết
Hướng dẫn này hiện đang hơi lỗi thời. Hầu hết các ví dụ đều sử dụng Options API, và không có quy tắc nào liên quan đến `<script setup>` và Composition API. Chúng tôi đang lên kế hoạch cải thiện nó trong tương lai.
:::

Đây là bản hướng dẫn về phong cách viết (style guide) chính thức dành cho code Vue. Nếu bạn dùng Vue trong dự án của mình, bản hướng dẫn này sẽ là một tham chiếu hữu ích giúp tránh xảy ra lỗi, anti-pattern, hoặc phí thời gian vào những tranh cãi không đáng có. Tất nhiên, chúng tôi cũng tin rằng không có style guide nào là hoàn hảo cho tất cả các team hoặc dự án, vì thế bạn nên điều chỉnh tùy theo kinh nghiệm, thiết lập kĩ thuật, và khả năng cá nhân của từng người.

Chúng tôi cũng sẽ tránh các đề nghị về JavaScript và HTML nói chung. Việc bạn dùng hay không dùng dấu chấm phẩy, dùng nháy đơn hay dấu kép cho thuộc tính HTML, đối với chúng tôi là không quan trọng. Tuy nhiên cũng sẽ có một số ngoại lệ khi chúng tôi nhận thấy một kiểu mẫu (pattern) cụ thể nào đó có thể có ích hơn trong ngữ cảnh của Vue.

Cuối cùng, chúng tôi cũng đã chia các quy tắc thành 4 loại:

## Phân loại quy tắc {#rule-categories}

### Ưu tiên mức A: Thiết yếu (Tránh gặp lỗi) {#priority-a-essential-error-prevention}

Các quy tắc này giúp tránh xảy ra lỗi, vì vậy hãy học và làm theo bằng bất cứ giá nào. Ngoại lệ nếu có cũng sẽ rất hiếm, và chỉ có thể xảy ra với những ai có kiến thức sâu sắc về cả JavaScript và Vue.

- [Xem tất cả các quy tắc ưu tiên mức A](./rules-essential)

### Ưu tiên mức B: Rất khuyên dùng {#priority-b-strongly-recommended}

Các quy tắc này giúp code dễ đọc hơn và/hoặc tăng cường trải nghiệm của lập trình viên trong phần lớn các dự án. Nếu bạn vi phạm các quy tắc này, code vẫn sẽ chạy, tuy nhiên nên tránh vi phạm trừ phi có lí do chính đáng.

- [Xem tất cả các quy tắc ưu tiên mức B](./rules-strongly-recommended)

### Ưu tiên mức C: Khuyên dùng {#priority-c-recommended}

Chúng ta có thể tự ý chọn một trong nhiều lựa chọn tốt ngang nhau để bảo đảm tính nhất quán. Trong các quy tắc này, chúng tôi sẽ mô tả từng lựa chọn được chấp nhận và đề nghị chọn một lựa chọn mặc định. Điều này có nghĩa là bạn hoàn toàn có thể chọn một lựa chọn khác cho dự án của mình, miễn là đảm bảo thống nhất và có lí do chính đáng. Việc có lí do chính đáng là rất quan trọng, vì khi tuân thủ theo tiêu chuẩn chung, bạn sẽ:

1. Luyện tập cho não bộ của bạn để dễ dàng đọc code của cộng đồng
2. Có thể copy và paste các ví dụ code từ cộng đồng mà không cần chỉnh sửa
3. Dễ tuyển được người đã quen với phong cách viết code mà bạn muốn, ít nhất là khi dùng Vue

- [Xem tất cả các quy tắc ưu tiên mức C](./rules-recommended)

### Ưu tiên mức D: Thận trọng khi dùng {#priority-d-use-with-caution}

Một vài tính năng của Vue tồn tại để phục vụ cho các trường hợp đặc biệt hoặc để dễ dàng chuyển đổi từ một codebase cũ (Có thể từ Vue 2). Tuy nhiên, khi sử dụng quá nhiều, chúng có thể làm cho code của bạn khó bảo trì hơn hoặc thậm chí nó sẽ trở thành lỗi trong tương lai. Những quy tắc này sẽ giúp bạn nhận ra những tính năng có thể gây rủi ro, mô tả khi nào và tại sao chúng nên được tránh sử dụng.

- [Xem tất cả các quy tắc ưu tiên mức D](./rules-use-with-caution)
