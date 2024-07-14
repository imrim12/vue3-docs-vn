# Slots {#slots}

> Trang này giả định bạn đã đọc qua [Cơ bản về Component](/guide/essentials/component-basics). Hãy đọc trước nếu bạn mới bắt đầu với component.

<VueSchoolLink href="https://vueschool.io/lessons/vue-3-component-slots" title="Free Vue.js Slots Lesson"/>

## Nội dung Slot Content và đầu ra {#slot-content-and-outlet}

Chúng ta đã học được rằng component có thể chấp nhận props, có thể là giá trị JavaScript của bất kỳ kiểu nào. Nhưng làm sao về nội dung template? Trong một số trường hợp, chúng ta có thể muốn truyền một đoạn template cho component con, và cho phép component con render đoạn template này trong template của nó.

Ví dụ, chúng ta có thể có một component `<FancyButton>` như sau:

```vue-html{2}
<FancyButton>
  Hãy nhấn vào tôi! <!-- nội dung của slot -->
</FancyButton>
```

Template của `<FancyButton>` có thể như sau:

```vue-html{2}
<button class="fancy-btn">
  <slot></slot> <!-- đầu ra của slot -->
</button>
```

Element `<slot>` là một **đầu ra slot** cho biết nơi mà **nội dung slot** được cung cấp bởi component cha sẽ được render.

![sơ đồ slot](./images/slots.png)

<!-- https://www.figma.com/file/LjKTYVL97Ck6TEmBbstavX/slot -->

Và đây là kết quả DOM cuối cùng:

```html
<button class="fancy-btn">Hãy nhấn vào tôi!</button>
```

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNpdUdlqAyEU/ZVbQ0kLMdNsXabTQFvoV8yLcRkkjopLSQj596oTwqRvnuM9y9UT+rR2/hs5qlHjqZM2gOch2m2rZW+NC/BDND1+xRCMBuFMD9N5NeKyeNrqphrUSZdA4L1VJPCEAJrRdCEAvpWke+g5NHcYg1cmADU6cB0A4zzThmYckqimupqiGfpXILe/zdwNhaki3n+0SOR5vAu6ReU++efUajtqYGJQ/FIg5w8Wt9FlOx+OKh/nV1c4ZVNqlHE1TIQQ7xnvCN13zkTNalBSc+Jw5wiTac2H1WLDeDeDyXrJVm9LWG7uE3hev3AhHge1cYwnO200L4QljEnd1bCxB1g82UNhe+I6qQs5kuGcE30NrxeaRudzOWtkemeXuHP5tLIKOv8BN+mw3w==)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNpdUdtOwzAM/RUThAbSurIbl1ImARJf0ZesSapoqROlKdo07d9x0jF1SHmIT+xzcY7sw7nZTy9Zwcqu9tqFTYW6ddYH+OZYHz77ECyC8raFySwfYXFsUiFAhXKfBoRUvDcBjhGtLbGgxNAVcLziOlVIp8wvelQE2TrDg6QKoBx1JwDgy+h6B62E8ibLoDM2kAAGoocsiz1VKMfmCCrzCymbsn/GY95rze1grja8694rpmJ/tg1YsfRO/FE134wc2D4YeTYQ9QeKa+mUrgsHE6+zC+vfjoz1Bdwqpd5iveX1rvG2R1GA0Si5zxrPhaaY98v5WshmCrerhVi+LmCxvqPiafUslXoYpq0XkuiQ1p4Ax4XQ2BSwdnuYP7p9QlvuG40JHI1lUaenv3o5w3Xvu2jOWU179oQNn5aisNMvLBvDOg==)

</div>

Với slot, `<FancyButton>` chịu trách nhiệm render `<button>` bên ngoài (và các style của nó), trong khi nội dung bên trong được cung cấp bởi component cha.

Một cách khác để hiểu slot là so sánh chúng với các hàm JavaScript:

```js
// component cha cung cấp nội dung cho slot
FancyButton('Click me!')

// FancyButton render nội dung slot trong template của nó
function FancyButton(slotContent) {
  return `<button class="fancy-btn">
      ${slotContent}
    </button>`
}
```

Nội dung slot không chỉ giới hạn ở text. Nó có thể là bất kỳ template content hợp lệ nào. Ví dụ, chúng ta có thể truyền vào nhiều element, hoặc thậm chí là các component khác:

```vue-html
<FancyButton>
  <span style="color:red">Click me!</span>
  <AwesomeIcon name="plus" />
</FancyButton>
```

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNp1UmtOwkAQvspQYtCEgrx81EqCJibeoX+W7bRZaHc3+1AI4QyewH8ewvN4Aa/gbgtNIfFf5+vMfI/ZXbCQcvBmMYiCWFPFpAGNxsp5wlkphTLwQjjdPlljBIdMiRJ6g2EL88O9pnnxjlqU+EpbzS3s0BwPaypH4gqDpSyIQVcBxK3VFQDwXDC6hhJdlZi4zf3fRKwl4aDNtsDHJKCiECqiW8KTYH5c1gEnwnUdJ9rCh/XeM6Z42AgN+sFZAj6+Ux/LOjFaEK2diMz3h0vjNfj/zokuhPFU3lTdfcpShVOZcJ+DZgHs/HxtCrpZlj34eknoOlfC8jSCgnEkKswVSRlyczkZzVLM+9CdjtPJ/RjGswtX3ExvMcuu6mmhUnTruOBYAZKkKeN5BDO5gdG13FRoSVTOeAW2xkLPY3UEdweYWqW9OCkYN6gctq9uXllx2Z09CJ9dJwzBascI7nBYihWDldUGMqEgdTVIq6TQqCEMfUpNSD+fX7/fH+3b7P8AdGP6wA==)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNptUltu2zAQvMpGQZEWsOzGiftQ1QBpgQK9g35oaikwkUiCj9aGkTPkBPnLIXKeXCBXyJKKBdoIoA/tYGd3doa74tqY+b+ARVXUjltp/FWj5GC09fCHKb79FbzXCoTVA5zNFxkWaWdT8/V/dHrAvzxrzrC3ZoBG4SYRWhQs9B52EeWapihU3lWwyxfPDgbfNYq+ejEppcLjYHrmkSqAOqMmAOB3L/ktDEhV4+v8gMR/l1M7wxQ4v+3xZ1Nw3Wtb8S1TTXG1H3cCJIO69oxc5mLUcrSrXkxSi1lxZGT0//CS9Wg875lzJELE/nLto4bko69dr31cFc8auw+3JHvSEfQ7nwbsHY9HwakQ4kes14zfdlYH1VbQS4XMlp1lraRMPl6cr1rsZnB6uWwvvi9hufpAxZfLryjEp5GtbYs0TlGICTCsbaXqKliZDZx/NpuEDsx2UiUwo5VxT6Dkv73BPFgXxRktlUdL2Jh6OoW8O3pX0buTsoTgaCNQcDjoGwk3wXkQ2tJLGzSYYI126KAso0uTSc8Pjy9P93k2d6+NyRKa)

</div>

Bằng cách sử dụng slot, `<FancyButton>` trở nên linh hoạt hơn. Chúng ta có thể sử dụng nó để render các button với nội dung khác nhau:

Cơ chế của slot cũng được lấy cảm hứng từ [native Web Component `<slot>` element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/slot), nhưng có thêm các khả năng mở rộng mà chúng ta sẽ thấy sau.

## Phạm vi Render {#render-scope}

Nội dung slot có quyền truy cập vào phạm vi dữ liệu của component cha, vì nó được định nghĩa trong component cha. Ví dụ:

```vue-html
<span>{{ message }}</span>
<FancyButton>{{ message }}</FancyButton>
```

Tại đây cả hai nội suy <span v-pre>`{{ message }}`</span> sẽ được render cùng một nội dung.

Nội dung slot **không** có quyền truy cập vào phạm vi dữ liệu của component con. Biểu thức trong template Vue chỉ có thể truy cập vào phạm vi mà nó được định nghĩa, tương tự như phạm vi của JavaScript. Nói cách khác:

> Biểu thức trong template cha chỉ có thể truy cập vào phạm vi của cha; biểu thức trong template con chỉ có thể truy cập vào phạm vi của con.

## Nội dung dự phòng {#fallback-content}

Có những trường hợp khi cần chỉ định nội dung dự phòng (mặc định) cho slot, chỉ được render khi không có nội dung được cung cấp. Ví dụ, trong component `<SubmitButton>`:

```vue-html
<button type="submit">
  <slot></slot>
</button>
```

Chúng ta có thể muốn text "Submit" được render trong `<button>` nếu component cha không cung cấp bất kỳ nội dung slot nào. Để làm cho "Submit" trở thành nội dung dự phòng, chúng ta có thể đặt nó giữa các thẻ `<slot>`:

```vue-html{3}
<button type="submit">
  <slot>
    Submit <!-- nội dung dự phòng -->
  </slot>
</button>
```

Bây giờ khi chúng ta sử dụng `<SubmitButton>` trong component cha, không cung cấp nội dung cho slot:

```vue-html
<SubmitButton />
```

Nó sẽ render nội dung dự phòng - "Submit":

```html
<button type="submit">Submit</button>
```

Nhưng nếu chúng ta cung cấp nội dung cho slot:

```vue-html
<SubmitButton>Save</SubmitButton>
```

Lúc này nội dung được cung cấp sẽ được render:

```html
<button type="submit">Save</button>
```

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNp1kMsKwjAQRX9lzMaNbfcSC/oL3WbT1ikU8yKZFEX8d5MGgi2YVeZxZ86dN7taWy8B2ZlxP7rZEnikYFuhZ2WNI+jCoGa6BSKjYXJGwbFufpNJfhSaN1kflTEgVFb2hDEC4IeqguARpl7KoR8fQPgkqKpc3Wxo1lxRWWeW+Y4wBk9x9V9d2/UL8g1XbOJN4WAntodOnrecQ2agl8WLYH7tFyw5olj10iR3EJ+gPCxDFluj0YS6EAqKR8mi9M3Td1ifLxWShcU=)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNp1UEEOwiAQ/MrKxYu1d4Mm+gWvXChuk0YKpCyNxvh3lxIb28SEA8zuDDPzEucQ9mNCcRAymqELdFKu64MfCK6p6Tu6JCLvoB18D9t9/Qtm4lY5AOXwMVFu2OpkCV4ZNZ51HDqKhwLAQjIjb+X4yHr+mh+EfbCakF8AclNVkCJCq61ttLkD4YOgqsp0YbGesJkVBj92NwSTIrH3v7zTVY8oF8F4SdazD7ET69S5rqXPpnigZ8CjEnHaVyInIp5G63O6XIGiIlZMzrGMd8RVfR0q4lIKKV+L+srW+wNTTZq3)

</div>

## Slot có tên {#named-slots}

Đôi khi có lúc cần có nhiều đầu ra slot trong một component duy nhất. Ví dụ, trong component `<BaseLayout>` với template như sau:

```vue-html
<div class="container">
  <header>
    <!-- Chúng ta muốn nội dung header ở đây -->
  </header>
  <main>
    <!-- Chúng ta muốn nội dung body ở đây -->
  </main>
  <footer>
    <!-- Chúng ta muốn nội dung footer ở đây -->
  </footer>
</div>
```

Đối với những trường hợp này, element `<slot>` có một thuộc tính đặc biệt là `name`, được sử dụng để gán một ID duy nhất cho các slot khác nhau để bạn có thể xác định nội dung sẽ được render ở đâu:

```vue-html
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```

Đầu ra `<slot>` mà không có `name` sẽ mặc định có tên là "default".

Trong một component cha sử dụng `<BaseLayout>`, chúng ta cần một cách để truyền nhiều đoạn template, mỗi đoạn template sẽ được render vào một đầu ra slot khác nhau. Đây là lúc **named slots** xuất hiện.

Để truyền nội dung cho một slot có tên, chúng ta cần sử dụng một element `<template>` với directive `v-slot`, và sau đó truyền tên của slot như một đối số cho `v-slot`:

```vue-html
<BaseLayout>
  <template v-slot:header>
    <!-- nội dung cho header slot -->
  </template>
</BaseLayout>
```

`v-slot` có một cú pháp rút gọn riêng biệt là `#`, vì vậy `<template v-slot:header>` có thể được rút gọn thành `<template #header>`. Hãy xem nó như là "render đoạn template này vào đầu ra slot 'header' của component con".

![sơ đồ slot có tên](./images/named-slots.png)

<!-- https://www.figma.com/file/2BhP8gVZevttBu9oUmUUyz/named-slot -->

Đây là đoạn code truyền nội dung cho tất cả các slot của `<BaseLayout>` sử dụng cú pháp rút gọn:

```vue-html
<BaseLayout>
  <template #header>
    <h1>Đây có thể là tiêu đề của trang</h1>
  </template>

  <template #default>
    <p>Một đoạn văn bản cho nội dung chính.</p>
    <p>Thêm một đoạn khác.</p>
  </template>

  <template #footer>
    <p>Một vài thông tin liên lạc ở đây</p>
  </template>
</BaseLayout>
```

Khi một component nhận cả slot mặc định và slot có tên, tất cả các node top-level không phải là `<template>` sẽ được xem như là nội dung cho default slot. Vì vậy đoạn code trên có thể được viết lại như sau:

```vue-html
<BaseLayout>
  <template #header>
    <h1>Đây có thể là tiêu đề của trang</h1>
  </template>

  <!-- Slot mặc định -->
  <p>Một đoạn văn bản cho nội dung chính.</p>
  <p>Thêm một đoạn khác.</p>

  <template #footer>
    <p>Một vài thông tin liên lạc ở đây</p>
  </template>
</BaseLayout>
```

Bây giờ tất cả mọi thứ bên trong `<template>` elements sẽ được truyền vào các slot tương ứng. Kết quả HTML cuối cùng sẽ là:

```html
<div class="container">
  <header>
    <h1>Đây có thể là tiêu đề của trang</h1>
  </header>
  <main>
    <p>Một đoạn văn bản cho nội dung chính.</p>
    <p>Thêm một đoạn khác.</p>
  </main>
  <footer>
    <p>Một vài thông tin liên lạc ở đây</p>
  </footer>
</div>
```

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNp9UsFuwjAM/RWrHLgMOi5o6jIkdtphn9BLSF0aKU2ixEVjiH+fm8JoQdvRfu/5xS8+ZVvvl4cOsyITUQXtCSJS5zel1a13geBdRvyUR9cR1MG1MF/mt1YvnZdW5IOWVVwQtt5IQq4AxI2cau5ccZg1KCsMlz4jzWrzgQGh1fuGYIcgwcs9AmkyKHKGLyPykcfD1Apr2ZmrHUN+s+U5Qe6D9A3ULgA1bCK1BeUsoaWlyPuVb3xbgbSOaQGcxRH8v3XtHI0X8mmfeYToWkxmUhFoW7s/JvblJLERmj1l0+T7T5tqK30AZWSMb2WW3LTFUGZXp/u8o3EEVrbI9AFjLn8mt38fN9GIPrSp/p4/Yoj7OMZ+A/boN9KInPeZZpAOLNLRDAsPZDgN4p0L/NQFOV/Ayn9x6EZXMFNKvQ4E5YwLBczW6/WlU3NIi6i/sYDn5Qu2qX1OF51MsvMPkrIEHg==)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNp9UkFuwjAQ/MoqHLiUpFxQlaZI9NRDn5CLSTbEkmNb9oKgiL934wRwQK3ky87O7njGPicba9PDHpM8KXzlpKV1qWVnjSP4FB6/xcnsCRpnOpin2R3qh+alBig1HgO9xkbsFcG5RyvDOzRq8vkAQLSury+l5lNkN1EuCDurBCFXAMWdH2pGrn2YtShqdCPOnXa5/kKH0MldS7BFEGDFDoEkKSwybo8rskjjaevo4L7Wrje8x4mdE7aFxjiglkWE1GxQE9tLi8xO+LoGoQ3THLD/qP2/dGMMxYZs8DP34E2HQUxUBFI35o+NfTlJLOomL8n04frXns7W8gCVEt5/lElQkxpdmVyVHvP2yhBo0SHThx5z+TEZvl1uMlP0oU3nH/kRo3iMI9Ybes960UyRsZ9pBuGDeTqpwfBAvn7NrXF81QUZm8PSHjl0JWuYVVX1PhAqo4zLYbZarUak4ZAWXv5gDq/pG3YBHn50EEkuv5irGBk=)

</div>

Thêm một lần nữa, bạn có thể hiểu về slot có tên bằng cách so sánh chúng với các hàm JavaScript:

```js
// truyền nhiều đoạn nội dung cho các slot có tên khác nhau
BaseLayout({
  header: `...`,
  default: `...`,
  footer: `...`
})

// <BaseLayout> render chúng ở các vị trí khác nhau
function BaseLayout(slots) {
  return `<div class="container">
      <header>${slots.header}</header>
      <main>${slots.default}</main>
      <footer>${slots.footer}</footer>
    </div>`
}
```

## Slot có tên "động" {#dynamic-slot-names}

Đối
[Tham số directive động](/guide/essentials/template-syntax.md#dynamic-arguments) cũng có thể được sử dụng với `v-slot` để định nghĩa tên slot động:

```vue-html
<base-layout>
  <template v-slot:[dynamicSlotName]>
    ...
  </template>

  <!-- with shorthand -->
  <template #[dynamicSlotName]>
    ...
  </template>
</base-layout>
```

Lưu ý rằng biểu thức phải tuân theo [ràng buộc về cú pháp](/guide/essentials/template-syntax.md#directives) của đối số directive động.

## Slot giới hạn {#scoped-slots}

Như đã thảo luận trong [Phạm vi Render](#render-scope), nội dung slot không có quyền truy cập vào phạm vi của component con.

Tuy nhiên, có những trường hợp mà nội dung slot có thể sử dụng dữ liệu từ cả phạm vi của component cha và component con. Để đạt được điều đó, chúng ta cần một cách để component con truyền dữ liệu cho slot khi render nó.

Thực tế, chúng ta có thể làm chính xác điều đó - chúng ta có thể truyền các thuộc tính cho một đầu ra slot giống như truyền props cho một component:

```vue-html
<!-- <MyComponent> template -->
<div>
  <slot :text="greetingMessage" :count="1"></slot>
</div>
```

Việc nhận các props này là một chút khác biệt khi sử dụng một default slot so với sử dụng các slot có tên. Chúng ta sẽ trước tiên xem cách nhận props khi sử dụng một default slot, bằng cách sử dụng `v-slot` trực tiếp trên thẻ component con:

```vue-html
<MyComponent v-slot="slotProps">
  {{ slotProps.text }} {{ slotProps.count }}
</MyComponent>
```

![sơ đồ slot giới hạn](./images/scoped-slots.svg)

<!-- https://www.figma.com/file/QRneoj8eIdL1kw3WQaaEyc/scoped-slot -->

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNp9kMEKgzAMhl8l9OJlU3aVOhg7C3uAXsRlTtC2tFE2pO++dA5xMnZqk+b/8/2dxMnadBxQ5EL62rWWwCMN9qh021vjCMrn2fBNoya4OdNDkmarXhQnSstsVrOOC8LedhVhrEiuHca97wwVSsTj4oz1SvAUgKJpgqWZEj4IQoCvZm0Gtgghzss1BDvIbFkqdmID+CNdbbQnaBwitbop0fuqQSgguWPXmX+JePe1HT/QMtJBHnE51MZOCcjfzPx04JxsydPzp2Szxxo7vABY1I/p)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNqFkNFqxCAQRX9l8CUttAl9DbZQ+rzQD/AlJLNpwKjoJGwJ/nvHpAnusrAg6FzHO567iE/nynlCUQsZWj84+lBmGJ31BKffL8sng4bg7O0IRVllWnpWKAOgDF7WBx2em0kTLElt975QbwLkhkmIyvCS1TGXC8LR6YYwVSTzH8yvQVt6VyJt3966oAR38XhaFjjEkvBCECNcia2d2CLyOACZQ7CDrI6h4kXcAF7lcg+za6h5et4JPdLkzV4B9B6RBtOfMISmxxqKH9TarrGtATxMgf/bDfM/qExEUCdEDuLGXAmoV06+euNs2JK7tyCrzSNHjX9aurQf)

</div>

Prop được truyền cho slot thông qua component con sẽ khả dụng như là một giá trị tương ứng với `v-slot` directive, và có thể được truy cập bởi các biểu thức trong slot.

Bạn có thể nghĩ về phạm vi slot như một hàm được truyền vào component con. Component con sau đó gọi nó, truyền props như là các đối số:

```js
MyComponent({
  // truyền nội dung cho slot, nhưng mà là truyền hàm
  default: (slotProps) => {
    return `${slotProps.text} ${slotProps.count}`
  }
})

function MyComponent(slots) {
  const greetingMessage = 'hello'
  return `<div>${
    // gọi hàm slot với props!
    slots.default({ text: greetingMessage, count: 1 })
  }</div>`
}
```

Thực tế, đây là cách slot giới hạn được biên dịch, và cách bạn sẽ sử dụng slot giới hạn trong [render functions](/guide/extras/render-function).

Chú ý cách `v-slot="slotProps"` khớp với hàm slot signature. Giống như với các đối số của hàm, chúng ta có thể sử dụng destructuring trong `v-slot`:

```vue-html
<MyComponent v-slot="{ text, count }">
  {{ text }} {{ count }}
</MyComponent>
```

### Slot giới hạn có tên {#named-scoped-slots}

Slot giới hạn có tên hoạt động tương tự như slot giới hạn mặc định - các slot prop có thể được như là giá trị của `v-slot` directive. Khi sử dụng cú pháp rút gọn, nó sẽ trông như thế này:

```vue-html
<MyComponent>
  <template #header="headerProps">
    {{ headerProps }}
  </template>

  <template #default="defaultProps">
    {{ defaultProps }}
  </template>

  <template #footer="footerProps">
    {{ footerProps }}
  </template>
</MyComponent>
```

Truyền prop cho slot có tên:

```vue-html
<slot name="header" message="hello"></slot>
```

Lưu ý rằng `name` của một slot sẽ không bao gồm trong props vì nó là một từ khóa - vì vậy `headerProps` sẽ là `{ message: 'hello' }`.

Nếu bạn đang trộn lẫn các slot có tên với slot giới hạn mặc định, bạn sẽ cần phải sử dụng một `<template>` element rõ ràng cho slot mặc định. Cố gắng đặt `v-slot` trực tiếp trên component sẽ dẫn đến lỗi biên dịch. Điều này nhằm tránh bất kỳ sự mơ hồ nào về phạm vi của props của slot mặc định. Ví dụ:

```vue-html
<!-- Template này sẽ không được biên dịch -->
<template>
  <MyComponent v-slot="{ message }">
    <p>{{ message }}</p>
    <template #footer>
      <!-- message thuộc về default slot và không khả dụng ở đây -->
      <p>{{ message }}</p>
    </template>
  </MyComponent>
</template>
```

Sử dụng một `<template>` element rõ ràng cho slot mặc định sẽ giúp làm rõ rằng `message` không khả dụng ở đây:

```vue-html
<template>
  <MyComponent>
    <!-- Sử dụng slot mặc định rõ ràng -->
    <template #default="{ message }">
      <p>{{ message }}</p>
    </template>

    <template #footer>
      <p>Đây là một vài thông tin liên lạc</p>
    </template>
  </MyComponent>
</template>
```

### Ví dụ về danh sách Fancy {#fancy-list-example}

Bạn có thể thắc mắc rằng đâu là trường hợp sử dụng slot giới hạn. Dưới đây là một ví dụ: giả sử chúng ta có một component `<FancyList>` thực hiện việc render một danh sách các item - nó có thể đóng gói logic cho việc tải dữ liệu từ xa, sử dụng dữ liệu để hiển thị danh sách, hoặc thậm chí là các tính năng nâng cao như 'phân trang' hoặc 'cuộn vô tận'. Tuy nhiên, chúng ta muốn nó linh hoạt với cách mỗi item trông như thế nào và để lại việc tạo kiểu cho mỗi item cho component cha sử dụng nó. Vì vậy, việc sử dụng có thể trông như thế này:

```vue-html
<FancyList :api-url="url" :per-page="10">
  <template #item="{ body, username, likes }">
    <div class="item">
      <p>{{ body }}</p>
      <p>by {{ username }} | {{ likes }} likes</p>
    </div>
  </template>
</FancyList>
```

Bên trong `<FancyList>`, chúng ta có thể render cùng một `<slot>` nhiều lần với dữ liệu item khác nhau (chú ý chúng ta đang sử dụng `v-bind` để truyền một object như là slot props):

```vue-html
<ul>
  <li v-for="item in items">
    <slot name="item" v-bind="item"></slot>
  </li>
</ul>
```

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNqFU2Fv0zAQ/StHJtROapNuZTBCNwnQQKBpTGxCQss+uMml8+bYlu2UlZL/zjlp0lQa40sU3/nd3Xv3vA7eax0uSwziYGZTw7UDi67Up4nkhVbGwScm09U5tw5yowoYhFEX8cBBImdRgyQMHRwWWjCHdAKYbdFM83FpxEkS0DcJINZoxpotkCIHkySo7xOixcMep19KrmGustUISotGsgJHIPgDWqg6DKEyvoRUMGsJ4HG9HGX16bqpAlU1izy5baqDFegYweYroMttMwLAHx/Y9Kyan36RWUTN2+mjXfpbrei8k6SjdSuBYFOlMaNI6AeAtcflSrqx5b8xhkl4jMU7H0yVUCaGvVeH8+PjKYWqWnpf5DQYBTtb+fc612Awh2qzzGaBiUyVpBVpo7SFE8gw5xIv/Wl4M9gsbjCCQbuywe3+FuXl9iiqO7xpElEEhUofKFQo2mTGiFiOLr3jcpFImuiaF6hKNxzuw8lpw7kuEy6ZKJGK3TR6NluLYXBVqwRXQjkLn0ueIc3TLonyZ0sm4acqKVovKIbDCVQjGsb1qvyg2telU4Yzz6eHv6ARBWdwjVqUNCbbFjqgQn6aW1J8RKfJhDg+5/lStG4QHJZjnpO5XjT0BMqFu+uZ81yxjEQJw7A1kOA76FyZjaWBy0akvu8tCQKeQ+d7wsy5zLpz1FlzU3kW1QP+x40ApWgWAySEJTv6/NitNMkllcTakwCaZZ5ADEf6cROas/RhYVQps5igEpkZLwzRROmG04OjDBcj7+Js+vYQDo9e0uH1qzeY5/s1vtaaqG969+vTTrsmBTMLLv12nuy7l+d5W673SBzxkzlfhPdWSXokdZMkSFWhuUDzTTtOnk6CuG2fBEwI9etrHXOmRLJUE0/vMH14In5vH30sCS4Nkr+WmARdztHQ6Jr02dUFPtJ/lyxUVgq6/UzyO1olSj9jc+0DcaWxe/fqab/UT51Uu7Znjw6lbUn5QWtR6vtJQM//4zPUt+NOw+lGzCqo/gLm1QS8)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNqNVNtq20AQ/ZWpQnECujhO0qaqY+hD25fQl4RCifKwllbKktXushcT1/W/d1bSSnYJNCCEZmbPmcuZ1S76olS6cTTKo6UpNVN2VQjWKqktfCOi3N4yY6HWsoVZmo0eD5kVAqAQ9KU7XNGaOG5h572lRAZBhTV574CJzJv7QuCzzMaMaFjaKk4sRQtgOeUmiiVO85siwncRQa6oThRpKHrO50XUnUdEwMMJw08M7mAtq20MzlAtSEtj4OyZGkweMIiq2AZKToxBgMcdxDCqVrueBfb7ZaaOQiOspZYgbL0FPBySIQD+eMeQc99/HJIsM0weqs+O258mjfZREE1jt5yCKaWiFXpSX0A/5loKmxj2m+YwT69p+7kXg0udw8nlYn19fYGufvSeZBXF0ZGmR2vwmrJKS4WiPswGWWYxzIIgs8fYH6mIJadnQXdNrdMiWAB+yJ7gsXdgLfjqcK10wtJqgmYZ+spnpGgl6up5oaa2fGKi6U8Yau9ZS6Wzpwi7WU1p7BMzaZcLbuBh0q2XM4fZXTc+uOPSGvjuWEWxlaAexr9uiIBf0qG3Uy6HxXwo9B+mn47CvbNSM+LHccDxAyvmjMA9Vdxh1WQiO0eywBVGEaN3Pj972wVxPKwOZ7BJWI2b+K5rOOVUNPbpYJNvJalwZmmahm3j7AhdSz3sPzDRS3R4SQwOCXxP4yVBzJqJarSzcY8H5mXWFfif1QVwPGjGcQWTLp7YrcLxCfyDdAuMW0cq30AOV+plcK1J+dxoXJkqR6igRCeNxjbxp3N6cX5V0Sb2K19dfFrA4uo9Gh8uP9K6Puvw3eyx9SH3IT/qPCZpiW6Y8Gq9mvekrutAN96o/V99ALPj)

</div>

### Renderless Component {#renderless-components}

Trong trường hợp `<FancyList>` trên đây, chúng ta đã thấy một component có thể được sử dụng để đóng gói hai logic có thể sử dụng lại (lấy dữ liệu từ xa và phân trang) và tạo kiểu, trong khi vẫn giữ cho component cha linh hoạt về cách mỗi item trông như thế nào.

Nếu chúng ta đẩy ý tưởng này xa hơn một chút, chúng ta có thể tạo ra một component chỉ đảm nhiệm việc đóng gói logic và không thực hiện render - trong khi đó hình ảnh đầu ra sẽ hoàn toàn do component cha quyết định thông qua slot giới hạn. Chúng ta gọi loại component này là **Renderless Component**.

Một ví dụ về Renderless Component là `<MouseTracker>` - một component không có template, nhưng nó sẽ theo dõi vị trí chuột hiện tại và truyền nó cho component cha thông qua slot giới hạn:

```vue-html
<MouseTracker v-slot="{ x, y }">
  Mouse is at: {{ x }}, {{ y }}
</MouseTracker>
```

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNqNUcFqhDAQ/ZUhF12w2rO4Cz301t5aaCEX0dki1SQko6uI/96J7i4qLPQQmHmZ9+Y9ZhQvxsRdiyIVmStsZQgcUmtOUlWN0ZbgXbcOP2xe/KKFs9UNBHGyBj09kCpLFj4zuSFsTJ0T+o6yjUb35GpNRylG6CMYYJKCpwAkzWNQOcgphZG/YZoiX/DQNAttFjMrS+6LRCT2rh6HGsHiOQKtmKIIS19+qmZpYLrmXIKxM1Vo5Yj9HD0vfD7ckGGF3LDWlOyHP/idYPQCfdzldTtjscl/8MuDww78lsqHVHdTYXjwCpdKlfoS52X52qGit8oRKrRhwHYdNrrDILouPbCNVZCtgJ1n/6Xx8JYAmT8epD3fr5cC0oGLQYpkd4zpD27R0vA=)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNqVUU1rwzAM/SvCl7SQJTuHdLDDbttthw18MbW6hjW2seU0oeS/T0lounQfUDBGepaenvxO4tG5rIkoClGGra8cPUhT1c56ghcbA756tf1EDztva0iy/Ds4NCbSAEiD7diicafigeA0oFvLPAYNhWICYEE5IL00fMp8Hs0JYe0OinDIqFyIaO7CwdJGihO0KXTcLriK59NYBlUARTyMn6Hv0yHgIp7ARAvl3FXm8yCRiuu1Fv/x23JakVqtz3t5pOjNOQNoC7hPz0nHyRSzEr7Ghxppb/XlZ6JjRlzhTAlA+ypkLWwAM6c+8G2BdzP+/pPbRkOoL/KOldH2mCmtnxr247kKhAb9KuHKgLVtMEkn2knG+sIVzV9sfmy8hfB/swHKwV0oWja4lQKKjoNOivzKrf4L/JPqaQ==)

</div>

Mặc dù đây là một pattern thú vị, hầu hết những gì đạt được với Renderless Component có thể đạt được một cách hiệu quả hơn với Composition API, mà không gây ra chi phí thêm do lồng component. Sau này, chúng ta sẽ thấy cách chúng ta có thể thực hiện cùng một chức năng 'theo dấu con trỏ' bằng việc sử dụng [Composable](/guide/reusability/composables).

Như đã đề cập, slot giới hạn vẫn rất hữu dụng trong các trường hợp mà chúng ta cần đóng gói logic **và** tạo kiểu cho đầu ra, như trong ví dụ `<FancyList>` ở trên.
