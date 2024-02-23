<script setup>
import TestingApiSwitcher from './TestingApiSwitcher.vue'
</script>

# Kiểm thử {#testing}

## Tại sao phải Kiểm thử? {#why-test}

Kiểm thử tự động giúp bạn và nhóm của bạn xây dựng các ứng dụng Vue phức tạp nhanh chóng và tự tin hơn bằng cách ngăn ngừa các lỗi và khuyến khích bạn phân tách ứng dụng của mình thành các hàm, module, lớp và thành phần có thể kiểm thử được. Giống như mọi ứng dụng khác, ứng dụng Vue mới của bạn có thể bị lỗi theo nhiều cách, quan trọng là bạn có thể phát hiện các vấn đề này và sửa chúng trước khi phát hành.

Trong hướng dẫn này, chúng tôi sẽ bao gồm các thuật ngữ cơ bản và đưa ra các đề xuất của chúng tôi về các công cụ nên chọn cho ứng dụng Vue 3 của bạn.

Có một phần đặc biệt dành cho Vue, nói về composables. Xem [Kiểm thử Composables](#testing-composables) bên dưới để biết thêm chi tiết.

## Khi nào nên Kiểm thử {#when-to-test}

Hãy kiểm thử từ sớm! Chúng tôi khuyến khích bạn bắt đầu viết các kiểm thử ngay khi bạn có thể. Càng lâu bạn chờ để thêm các kiểm thử vào ứng dụng của mình, thì ứng dụng của bạn sẽ có nhiều phụ thuộc hơn và càng khó để bắt đầu.

## Các kiểu Kiểm thử {#testing-types}

Khi thiết kế chiến lược kiểm thử cho ứng dụng Vue của bạn, bạn nên tận dụng các kiểu kiểm thử sau:

- **Unit**: Kiểm tra các đầu vào của một hàm, lớp hoặc composables cụ thể có tạo ra đầu ra hoặc các tác động phụ như mong đợi hay không.
- **Component**: Kiểm tra rằng thành phần của bạn có được mount, render, có thể tương tác và hoạt động như mong đợi hay không. Những kiểm thử này sẽ import nhiều code hơn, phức tạp hơn và yêu cầu thời gian thực thi nhiều hơn.
- **End-to-end**: Kiểm tra các tính năng mà bao gồm nhiều trang và thực hiện các yêu cầu mạng thực tế đối với ứng dụng Vue được xây dựng cho sản phẩm. Những kiểm thử này thường liên quan đến việc triển khai một cơ sở dữ liệu hoặc các backend khác.

Mỗi kiểu kiểm thử đóng một vai trò trong chiến lược kiểm thử của ứng dụng của bạn và mỗi kiểu sẽ bảo vệ bạn khỏi các vấn đề khác nhau.

## Tổng quát {#overview}

Chúng ta sẽ thảo luận ngắn gọn về những gì mỗi kiểu kiểm thử, và cách chúng có thể được triển khai cho các ứng dụng Vue và cung cấp một số đề xuất chung.

## Unit Testing {#unit-testing}

Unit test được viết để xác minh rằng các đơn vị nhỏ, cô lập của code đang hoạt động như mong đợi. Một unit test thường bao gồm một hàm, lớp, composables hoặc module. Unit test tập trung vào tính chính xác logic và chỉ quan tâm đến một phần nhỏ trong các chức năng tổng thể của ứng dụng. Chúng có thể mô phỏng một phần lớn môi trường của ứng dụng của bạn (ví dụ: trạng thái ban đầu, các lớp phức tạp, các module bên thứ ba và các yêu cầu mạng).

Nhìn chung, unit test sẽ bắt các vấn đề về logic kinh doanh và tính chính xác logic của một hàm.

Ví dụ, hãy xem xét hàm `increment` này:

```js
// helpers.js
export function increment (current, max = 10) {
  if (current < max) {
    return current + 1
  }
  return current
}
```

Bởi vì đây là một hàm tự chứa, nó sẽ dễ dàng để gọi hàm `increment` và kiểm tra rằng nó trả về những gì nó cần trả về, vì vậy chúng ta sẽ viết một Unit Test.

Nếu như bất kỳ kiểm tra nào thất bại, rõ ràng rằng vấn đề nằm trong hàm `increment`.

```js{4-16}
// helpers.spec.js
import { increment } from './helpers'

describe('increment', () => {
  test('increments the current number by 1', () => {
    expect(increment(0, 10)).toBe(1)
  })

  test('does not increment the current number over the max', () => {
    expect(increment(10, 10)).toBe(10)
  })

  test('has a default max of 10', () => {
    expect(increment(10)).toBe(10)
  })
})
```

Như đã đề cập trước đó, unit test thường được áp dụng cho logic kinh doanh độc lập, các thành phần, lớp, module hoặc hàm không liên quan đến việc hiển thị UI, các yêu cầu mạng hoặc các vấn đề môi trường khác.

Đây thường là các module JavaScript / TypeScript đơn giản không liên quan đến Vue. Nói chung, viết unit test cho logic kinh doanh trong các ứng dụng Vue không khác nhiều so với các ứng dụng sử dụng các framework khác.

Có hai trường hợp mà bạn CÓ thể viết unit test cho các tính năng cụ thể của Vue:

1. Composables
2. Components

### Composables {#composables}

Một danh mục các hàm đặc biệt cho các ứng dụng Vue là [Composables](/guide/reusability/composables), có thể yêu cầu xử lý đặc biệt trong quá trình kiểm thử. 
Xem [Kiểm thử Composables](#testing-composables) bên dưới để biết thêm chi tiết.

### Unit Testing Components {#unit-testing-components}

Một component có thể được kiểm thử theo hai cách:
1. Whitebox: Unit Testing

   Test được gọi là "Whitebox tests" có ý thức về chi tiết cài đặt và các phụ thuộc của một component. Chúng tập trung vào **cô lập** component đang được kiểm thử. Những kiểm thử này thường liên quan đến việc giả lập một số hoặc tất cả các component con của bạn, cũng như thiết lập trạng thái và các phụ thuộc của plugin (ví dụ: Pinia).

2. Blackbox: Component Testing

   Test được gọi là "Blackbox tests" không có ý thức về chi tiết cài đặt của một component. Những kiểm thử này giả định rằng component đang được kiểm thử là một đơn vị độc lập và không giả định bất kỳ thông tin nào về các component con của nó. Những kiểm thử này thường liên quan đến việc render tất cả các component con của bạn và được coi là một "kiểm thử tích hợp". Xem [Các khuyến nghị đối với kiểm thử Component](#component-testing) bên dưới.

### Khuyến nghị {#recommendation}

- [Vitest](https://vitest.dev/)

  Từ khi thiết lập chính thức được tạo bởi `create-vue` dựa trên [Vite](https://vitejs.dev/), chúng tôi khuyến khích sử dụng một framework kiểm thử đơn vị có thể tận dụng cùng cấu hình và chuyển đổi pipeline trực tiếp từ Vite. [Vitest](https://vitest.dev/) là một framework kiểm thử unit được thiết kế đặc biệt cho mục đích này, được tạo và duy trì bởi các thành viên của nhóm Vue / Vite. Nó tích hợp với các dự án dựa trên Vite với sự cố gắng tối thiểu và rất nhanh chóng.

### Những lựa chọn khác {#other-options}

- [Peeky](https://peeky.dev/) là một framework kiểm thử unit nhanh chóng với tích hợp Vite đầu tiên. Nó được tạo bởi một thành viên của nhóm Vue / Vite.

- [Jest](https://jestjs.io/) là một framework kiểm thử unit phổ biến, và có thể được làm việc với Vite thông qua gói [vite-jest](https://github.com/sodatea/vite-jest). Tuy nhiên, chúng tôi chỉ khuyến khích Jest nếu bạn có một bộ kiểm thử Jest hiện có cần được di chuyển sang một dự án dựa trên Vite, vì Vitest cung cấp một tích hợp mượt mà hơn và hiệu suất tốt hơn.

## Kiểm thử Component {#component-testing}

Trong ứng dụng Vue, component là những viên gạch xây dựng nên UI. Do đó, component là đơn vị cô lập tự nhiên khi nói đến việc xác minh hành vi của ứng dụng của bạn. Từ một góc độ độ phân mảnh, kiểm thử component nằm ở một nơi nào đó trên kiểm thử unit và có thể được coi là một dạng kiểm thử tích hợp. Rất nhiều ứng dụng Vue của bạn nên được bao phủ bởi một kiểm thử component và chúng tôi khuyến khích mỗi component Vue có một tệp cấu hình riêng của nó.

Component test nên bắt các lỗi liên quan đến các props, events, slots, styles, classes, lifecycle hooks và nhiều hơn nữa.

Component test không nên giả lập các component con, mà thay vào đó nên kiểm tra các tương tác giữa component của bạn và các component con của nó bằng cách tương tác với các component như một người dùng sẽ làm. Ví dụ, một component test nên click vào một phần tử như một người dùng thay vì tương tác với component như một lập trình viên.

Component test nên tập trung vào các giao diện công khai của component thay vì các chi tiết cài đặt bên trong. Đối với hầu hết các component, giao diện công khai bị giới hạn trong các thành phần sau: events được phát ra, props và slots. Khi kiểm thử, hãy nhớ **kiểm thử những gì một component làm, chứ không phải làm thế nào nó làm được**.

**HÃY THỰC HIỆN**

- Đối với logic **Hiển thị**: kiểm tra đầu ra render chính xác dựa trên props và slots được nhập.
- Đối với logic **Hành vi**: kiểm tra đầu ra render được cập nhật chính xác hoặc các sự kiện được phát ra dựa trên các sự kiện đầu vào của người dùng.

  Trong ví dụ dưới đây, chúng tôi thực hiện một component Stepper có một phần tử DOM được gắn nhãn là "increment" và có thể được click. Chúng tôi truyền một prop được gọi là `max` để ngăn Stepper được tăng lên quá `2`, vì vậy nếu chúng ta click vào nút 3 lần, giao diện người dùng vẫn nên hiển thị `2`.

  Chúng ta không biết gì về cài đặt của Stepper, chỉ biết rằng "input" là prop `max` và "output" là trạng thái của DOM khi người dùng nhìn thấy nó.

<TestingApiSwitcher>

<div class="testing-library-api">

```js
const { getByText } = render(Stepper, {
  props: {
    max: 1
  }
})

getByText('0') // kiểm tra rõ ràng rằng "0" nằm trong component

const button = getByText('increment')

// Gửi một sự kiện click đến nút increment.
await fireEvent.click(button)

getByText('1')

await fireEvent.click(button)
```

</div>

<div class="vtu-api">

```js
const valueSelector = '[data-testid=stepper-value]'
const buttonSelector = '[data-testid=increment]'

const wrapper = mount(Stepper, {
  props: {
    max: 1
  }
})

expect(wrapper.find(valueSelector).text()).toContain('0')

await wrapper.find(buttonSelector).trigger('click')

expect(wrapper.find(valueSelector).text()).toContain('1')
```

</div>

<div class="cypress-api">

```js
const valueSelector = '[data-testid=stepper-value]'
const buttonSelector = '[data-testid=increment]'

mount(Stepper, {
  props: {
    max: 1
  }
})

cy.get(valueSelector).should('be.visible').and('contain.text', '0')
  .get(buttonSelector).click()
  .get(valueSelector).should('contain.text', '1')
```

</div>

</TestingApiSwitcher>

- **ĐỪNG THỰC HIỆN**

  Đừng kiểm tra các state riêng tư của một instance component hoặc kiểm tra các phương thức riêng tư của một component. Kiểm tra các chi tiết cài đặt làm cho các kiểm thử dễ bị hỏng, vì chúng có khả năng bị hỏng và yêu cầu cập nhật khi cài đặt thay đổi.

  Công việc cuối cùng của component là render đầu ra DOM chính xác, vì vậy các kiểm thử tập trung vào đầu ra DOM cung cấp cùng một mức độ độ tin cậy trong khi vẫn linh hoạt và mạnh mẽ đối với các thay đổi.

  Đừng phụ thuộc hoàn toàn vào các kiểm thử snapshot. Việc kiểm tra chuỗi HTML không mô tả độ chính xác. Hãy viết các kiểm thử với ý định.

  Nếu một phương thức cần được kiểm tra kỹ lưỡng, hãy xem xét trích xuất nó thành một hàm tiện ích độc lập và viết một unit test riêng cho nó. Nếu nó không thể được trích xuất một cách sạch sẽ, nó có thể được kiểm tra như một phần của một component, kiểm tra tích hợp hoặc kiểm tra cuối cùng.

### Khuyến nghị {#recommendation-1}

- [Vitest](https://vitest.dev/) đối với các component hoặc composables render headlessly (ví dụ: [`useFavicon`](https://vueuse.org/core/useFavicon/#usefavicon) trong VueUse). Component và DOM có thể được kiểm tra bằng [`@vue/test-utils`](https://github.com/vuejs/test-utils).

- [Cypress Component Testing](https://on.cypress.io/component) đối với các component có các hành vi phụ thuộc vào việc hiển thị các style hoặc kích hoạt các sự kiện DOM. Có thể được sử dụng với Testing Library thông qua [@testing-library/cypress](https://testing-library.com/docs/cypress).

Sự khác biệt chính giữa Vitest và các trình chạy dựa trên trình duyệt là tốc độ và ngữ cảnh thực thi. Nói ngắn gọn, các trình chạy dựa trên trình duyệt, chẳng hạn như Cypress, có thể bắt các vấn đề mà các trình chạy dựa trên node, chẳng hạn như Vitest, không thể bắt được (ví dụ: các vấn đề về style, các sự kiện DOM thực sự, cookie, lưu trữ cục bộ và lỗi mạng), nhưng các trình chạy dựa trên trình duyệt là _nhiều lần chậm hơn Vitest_ vì chúng mở một trình duyệt, biên dịch các bảng kiểu của bạn và nhiều hơn nữa. Cypress là một trình chạy dựa trên trình duyệt hỗ trợ kiểm thử component. Vui lòng đọc [trang so sánh của Vitest](https://vitest.dev/guide/comparisons.html#cypress) để biết thông tin mới nhất so sánh Vitest và Cypress.

### Thư viện Mounting {#mounting-libraries}

Kiểm thử component thường bao gồm việc mount component được kiểm thử trong môi trường tách biệt, giả lập các sự kiện input của người dùng và kiểm tra DOM đầu ra. Có nhiều thư viện hữu ích được thiết kế riêng để giúp bạn thực hiện các tác vụ này một cách dễ dàng hơn.

- [`@vue/test-utils`](https://github.com/vuejs/test-utils) là thư viện kiểm thử component low-level chính thức được viết để cung cấp cho người dùng truy cập vào các API cụ thể của Vue. Nó cũng là thư viện low-level mà `@testing-library/vue` được xây dựng trên.

- [`@testing-library/vue`](https://github.com/testing-library/vue-testing-library) là một thư viện kiểm thử Vue tập trung vào việc kiểm thử component mà không cần dựa vào chi tiết cài đặt. Nguyên tắc chỉ đạo của nó là "càng giống với cách người dùng sử dụng phần mềm, thì càng có thể tin tưởng".

Chúng tôi khuyến khích sử dụng `@vue/test-utils` cho việc kiểm thử component trong ứng dụng. `@testing-library/vue` có các vấn đề với việc kiểm thử component bất đồng bộ với Suspense, vì vậy nó nên được sử dụng một cách cẩn thận.

### Những lựa chọn khác {#other-options-1}

- [Nightwatch](https://nightwatchjs.org/) là một trình chạy kiểm thử E2E được thiết kế để hỗ trợ Kiểm thử Component Vue. ([Dự án mẫu](https://github.com/nightwatchjs-community/todo-vue))

- [WebdriverIO](https://webdriver.io/docs/component-testing/vue) được sử dụng cho kiểm thử component đa trình duyệt với phụ thuộc vào hành vi tương tác thuần tuý của người dùng dựa trên tự động hoá chuẩn hoá. Thư viện này cũng có thể được sử dụng với Testing Library.

## Kiểm thử E2E {#e2e-testing}

Mặc dù unit test cung cấp cho các nhà phát triển một số độ tin cậy nhất định, nhưng unit test và kiểm thử component có giới hạn về khả năng bao phủ toàn diện một ứng dụng khi triển khai vào production. Kết quả là, kiểm thử end-to-end (E2E) cung cấp một mức độ bao phủ toàn diện hơn về ứng dụng của bạn khi triển khai vào production: những gì xảy ra khi người dùng thực sự sử dụng ứng dụng của bạn.

Kiểm thử End-to-end tập trung vào hành vi của ứng dụng đa trang, bao gồm việc thực hiện các yêu cầu mạng đối với ứng dụng Vue phiên bản production. Chúng thường liên quan đến việc triển khai một cơ sở dữ liệu hoặc các backend khác và có thể được chạy trên môi trường Staging.

Kiểm thử End-to-end thường bắt các vấn đề về router, thư viện quản lý state, các component cấp cao (ví dụ: App hoặc Layout), public assets, hoặc bất kỳ yêu cầu xử lý nào. Như đã nói ở trên, chúng bắt các vấn đề quan trọng mà có thể không thể bắt được bằng các unit test hoặc kiểm thử component.

Kiểm thử End-to-end không import bất kỳ code ứng dụng Vue của bạn, mà thay vào đó hoàn toàn dựa vào việc kiểm thử ứng dụng của bạn bằng cách điều hướng qua các trang trong một trình duyệt thực sự.

Kiểm thử End-to-end xác minh nhiều lớp trong ứng dụng của bạn. Chúng có thể nhắm mục tiêu ứng dụng được xây dựng trên máy cục bộ của bạn, hoặc thậm chí là một môi trường Staging trực tiếp. Kiểm thử đối với môi trường Staging không chỉ bao gồm code frontend và static server, mà còn bao gồm tất cả các dịch vụ backend và cơ sở hạ tầng liên quan.

> Các kiểm thử của bạn càng giống với cách người dùng sử dụng phần mềm, thì càng có thể tin tưởng những kiểm thử đó. - [Kent C. Dodds](https://twitter.com/kentcdodds/status/977018512689455106) - Tác giả của Testing Library

Bằng cách kiểm thử cách người dùng có thể tác động vào ứng dụng của bạn dựa trên các hành động khác nhau, kiểm thử E2E thường cung cấp một mức độ tin cậy cao hơn về việc ứng dụng của bạn có hoạt động đúng cách hay không.

### Chọn một giải pháp Kiểm thử E2E {#choosing-an-e2e-testing-solution}

Mặc dù kiểm thử end-to-end (E2E) trên web đã có một danh tiếng tiêu cực về các kiểm thử không đáng tin cậy (flaky) và làm chậm quá trình phát triển, nhưng các công cụ E2E hiện đại đã tiến xa hơn để tạo ra các kiểm thử đáng tin cậy, tương tác và hữu ích hơn. Khi chọn một framework kiểm thử E2E, các phần sau sẽ cung cấp một số hướng dẫn về những điều cần lưu ý khi chọn một framework kiểm thử cho ứng dụng của bạn.

#### Kiểm thử đa trình duyệt {#cross-browser-testing}

Một trong những lợi ích chính mà kiểm thử end-to-end (E2E) được biết đến là khả năng kiểm thử ứng dụng của bạn trên nhiều trình duyệt. Mặc dù có vẻ hấp dẫn khi có thể 100% phủ sóng đa trình duyệt, nhưng điều quan trọng cần lưu ý là kiểm thử đa trình duyệt có hiệu quả giảm dần về tài nguyên của một nhóm do thời gian và sức mạnh máy tính bổ sung cần thiết để chạy chúng một cách nhất quán. Kết quả là, việc lựa chọn số lượng kiểm thử đa trình duyệt cho ứng dụng của bạn là rất quan trọng.

#### Quy trình phản hồi nhanh hơn {#faster-feedback-loops}

Một trong những vấn đề chính với kiểm thử end-to-end (E2E) và phát triển là việc chạy toàn bộ bộ kiểm thử E2E mất rất nhiều thời gian. Thông thường, các bộ kiểm thử E2E được chạy trong các pipeline liên tục tích hợp và triển khai (CI/CD). Các framework kiểm thử E2E hiện đại đã giúp giải quyết vấn đề này bằng cách thêm các tính năng như song song hóa, cho phép các pipeline CI/CD thường chạy nhanh hơn nhiều lần so với trước đây. Ngoài ra, khi phát triển cục bộ, khả năng chạy một kiểm thử duy nhất cho trang bạn đang làm việc trên trong khi cung cấp hot reloading của các kiểm thử có thể giúp tăng quy trình phản hồi nhanh hơn của nhà phát triển.

#### Trải nghiệm debugging hạng nhất {#first-class-debugging-experience}

Mặc dù các nhà phát triển có truyền thống phụ vào việc kiểm tra các logs trong cửa sổ terminal để giúp xác định lỗi sai trong một kiểm thử, nhưng các framework kiểm thử end-to-end (E2E) hiện đại cho phép các nhà phát triển tận dụng các công cụ mà họ đã quen thuộc, ví dụ: các công cụ phát triển trình duyệt.

#### Mức độ hiển thị trong chế độ headless {#visibility-in-headless-mode}

Khi kiểm thử end-to-end (E2E) được chạy trong các pipeline liên tục tích hợp và triển khai (CI/CD), chúng thường được chạy trong các trình duyệt headless (tức là không có trình duyệt hiển thị được mở cho người dùng xem). Một tính năng quan trọng của các framework kiểm thử E2E hiện đại là khả năng xem các snapshot và / hoặc video của ứng dụng trong quá trình kiểm thử, cung cấp một số thông tin về lý do tại sao các lỗi đang xảy ra. Theo lịch sử phát triển, việc duy trì các tích hợp này rất là tẻ nhạt.

### Khuyến nghị {#recommendation-2}

- [Cypress](https://www.cypress.io/)

  Nhìn chung, chúng tôi tin rằng Cypress là một giải pháp kiểm thử E2E tốt nhất cho ứng dụng Vue với các tính năng như giao diện biểu đồ thông tin, khả năng debug tuyệt vời, các kiểm thử và code giả được tích hợp sẵn, khả năng chống lỗi lệch kết quả, song song hoá, snapshot. Như đã đề cập ở trên, nó cũng hỗ trợ [Kiểm thử Component](https://on.cypress.io/component). Tuy nhiên, nó chỉ hỗ trợ các trình duyệt dựa trên Chromium và Firefox.

### Những lựa chọn khác {#other-options-2}

- [Playwright](https://playwright.dev/) cũng là một giải pháp kiểm thử E2E tốt với việc hỗ trợ nhiều trình duyệt khác nhau (chủ yếu là WebKit). Xem [Why Playwright](https://playwright.dev/docs/why-playwright) để biết thêm chi tiết.

- [Nightwatch](https://nightwatchjs.org/) là một giải pháp kiểm thử E2E dựa trên [Selenium WebDriver](https://www.npmjs.com/package/selenium-webdriver). Đây là thư viện kiểm thử hỗ trợ nhiều trình duyệt nhất.

- [WebdriverIO](https://webdriver.io/) là một framework tự động cho việc kiểm thử web và mobile dựa trên giao thức WebDriver.

## Cách thực hiện {#recipes}

### Thêm Vitest vào một dự án {#adding-vitest-to-a-project} 

Trong một dự án Vue dựa trên Vite, chạy:

```sh
> npm install -D vitest happy-dom @testing-library/vue
```

Tiếp theo, cập nhật cấu hình Vite để thêm khối tùy chọn `test`:

```js{6-12}
// vite.config.js
import { defineConfig } from 'vite'

export default defineConfig({
  // ...
  test: {
    // bật các API test global giống như jest
    globals: true,
    // giả lập DOM với happy-dom
    // (yêu cầu cài đặt happy-dom như một peer dependency)
    environment: 'happy-dom'
  }
})
```

:::tip
Nếu như bạn đang sử dụng TypeScript, hãy thêm `vitest/globals` vào trường `types` trong `tsconfig.json`.

```json
// tsconfig.json

{
  "compilerOptions": {
    "types": ["vitest/globals"]
  }
}
```

:::

Kế đến, tạo một file kết thúc bằng `*.test.js` trong dự án của bạn. Bạn có thể đặt tất cả các file test trong một thư mục test trong thư mục gốc của dự án, hoặc trong các thư mục test bên cạnh các file nguồn của bạn. Vitest sẽ tự động tìm kiếm chúng bằng cách sử dụng quy ước đặt tên.

```js
// MyComponent.test.js
import { render } from '@testing-library/vue'
import MyComponent from './MyComponent.vue'

test('it should work', () => {
  const { getByText } = render(MyComponent, {
    props: {
      /* ... */
    }
  })

  // kiểm tra đầu ra
  getByText('...')
})
```

Cuối cùng, cập nhật `package.json` để thêm script test và chạy nó:

```json{4}
{
  // ...
  "scripts": {
    "test": "vitest"
  }
}
```

```sh
> npm test
```

### Kiểm thử Composable {#testing-composables}

> Phần này giả định bạn đã đọc qua [Composables](/guide/reusability/composables).

Khi nói đến kiểm thử composables, chúng ta có thể chia chúng thành hai loại: composables không phụ thuộc vào một instance component chủ, và composables phụ thuộc vào một instance component chủ.

Một composable phụ thuộc vào một instance component chủ khi nó sử dụng các API sau:

- Lifecycle hooks
- Provide / Inject

Nếu một composable chỉ sử dụng các API Reactivity, nó có thể được kiểm thử bằng cách gọi trực tiếp nó và xác minh trạng thái / phương thức được trả về:

```js
// counter.js
import { ref } from 'vue'

export function useCounter() {
  const count = ref(0)
  const increment = () => count.value++

  return {
    count,
    increment
  }
}
```

```js
// counter.test.js
import { useCounter } from './counter.js'

test('useCounter', () => {
  const { count, increment } = useCounter()
  expect(count.value).toBe(0)

  increment()
  expect(count.value).toBe(1)
})
```
Một composable phụ thuộc vào lifecycle hooks hoặc Provide / Inject cần được bao bọc trong một component chủ để được kiểm thử. Chúng ta có thể tạo một helper như sau:

```js
// test-utils.js
import { createApp } from 'vue'

export function withSetup(composable) {
  let result
  const app = createApp({
    setup() {
      result = composable()
      // Ẩn cảnh báo template bị thiếu
      return () => {}
    }
  })
  app.mount(document.createElement('div'))
  // trả về kết quả và instance app
  // để kiểm thử provide / unmount
  return [result, app]
}
```

```js
import { withSetup } from './test-utils'
import { useFoo } from './foo'

test('useFoo', () => {
  const [result, app] = withSetup(() => useFoo(123))
  // giả lập provide để kiểm thử injections
  app.provide(...)
  // chạy kiểm thử
  expect(result.foo.value).toBe(1)
  // kích hoạt onUnmounted hook nếu cần thiết
  app.unmount()
})
```

Đối với các composable phức tạp, việc kiểm thử có thể trở nên dễ dàng hơn bằng cách viết các kiểm thử cho component wrapper sử dụng composable đó bằng các kỹ thuật [Component Testing](#component-testing).

<!--
TODO more testing recipes can be added in the future e.g.
- How to set up CI via GitHub actions
- How to do mocking in component testing
-->
