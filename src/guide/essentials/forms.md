---
outline: deep
---

<script setup>
import { ref } from 'vue'
const message = ref('')
const multilineText = ref('')
const checked = ref(false)
const checkedNames = ref([])
const picked = ref('')
const selected = ref('')
const multiSelected = ref([])
</script>

# Ràng buộc Form Input {#form-input-bindings}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/user-inputs-vue-devtools-in-vue-3" title="Free Lesson on User Inputs with Vue.js"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-user-inputs-in-vue" title="Free Lesson on User Inputs with Vue.js"/>
</div>

Khi xử lý form trên frontend, chúng ta thường cần đồng bộ hóa trạng thái của các phần tử input form với trạng thái tương ứng trong JavaScript. Nó có thể rất phiền khi phải kết nối các giá trị và lắng nghe sự kiện thay đổi một cách thủ công:

```vue-html
<input
  :value="text"
  @input="event => text = event.target.value">
```

`v-model` directive giúp chúng ta đơn giản hóa ví dụ trên thành:

```vue-html
<input v-model="text">
```

Thêm vào đó, `v-model` còn có thể được sử dụng trên khác loại input khác nhau, `<textarea>` và `<select>`. Nó tự động mở rộng cho các thuộc tính DOM và các cặp sự kiện khác nhau dựa trên phần tử mà nó được sử dụng:

- `<input>` với kiểu text và các element `<textarea>` sử dụng thuộc tính `value` và sự kiện `input`;
- `<input type="checkbox">` và `<input type="radio">` sử dụng thuộc tính `checked` và sự kiện `change`;
- `<select>` sử dụng thuộc tính `value` và sự kiện `change`.

::: tip Lưu ý
`v-model` sẽ bỏ qua các thuộc tính `value`, `checked` hoặc `selected` ban đầu được tìm thấy trên bất kỳ phần tử form nào. Nó luôn luôn coi trạng thái JavaScript hiện tại được ràng buộc là nguồn tin chính. Bạn nên khai báo giá trị ban đầu trên phía JavaScript, sử dụng <span class="options-api">tùy chọn [`data`](/api/options-state.html#data)</span><span class="composition-api">[reactivity APIs](/api/reactivity-core.html#reactivity-api-core)</span>.
:::

## Ứng dụng cơ bản {#basic-usage}

### Text {#text}

```vue-html
<p>Message is: {{ message }}</p>
<input v-model="message" placeholder="sửa tôi đi" />
```

<div class="demo">
  <p>Message is: {{ message }}</p>
  <input v-model="message" placeholder="sửa tôi đi" />
</div>

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNo9jUEOgyAQRa8yYUO7aNkbNOkBegM2RseWRGACoxvC3TumxuX/+f+9ql5Ez31D1SlbpuyJoSBvNLjoA6XMUCHjAg2WnAJomWoXXZxSLAwBSxk/CP2xuWl9d9GaP0YAEhgDrSOjJABLw/s8+NJBrde/NWsOpWPrI20M+yOkGdfeqXPiFAhowm9aZ8zS4+wPv/RGjtZcJtV+YpNK1g==)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNo9jdEKwjAMRX8l9EV90L2POvAD/IO+lDVqoetCmw6h9N/NmBuEJPeSc1PVg+i2FFS90nlMnngwEb80JwaHL1sCQzURwFm258u2AyTkkuKuACbM2b6xh9Nps9o6pEnp7ggWwThRsIyiADQNz40En3uodQ+C1nRHK8HaRyoMy3WaHYa7Uf8To0CCRvzMwWESH51n4cXvBNTd8Um1H0FuTq0=)

</div>

<span id="vmodel-ime-tip"></span>
::: tip Lưu ý
Đối với các ngôn ngữ yêu cầu [IME](https://en.wikipedia.org/wiki/Input_method) (Chinese, Japanese, Korean etc.), bạn sẽ nhận thấy `v-model` không được cập nhật trong quá trình IME composition. Nếu bạn muốn phản hồi với các cập nhật này, hãy sử dụng lắng nghe sự kiện `input` và ràng buộc `value` của riêng bạn thay vì sử dụng `v-model`.
:::

### Multiline text {#multiline-text}

```vue-html
<span>Multiline message là:</span>
<p style="white-space: pre-line;">{{ message }}</p>
<textarea v-model="message" placeholder="thêm nhiều dòng vào đây"></textarea>
```

<div class="demo">
  <span>Multiline message là:</span>
  <p style="white-space: pre-line;">{{ multilineText }}</p>
  <textarea v-model="multilineText" placeholder="thêm nhiều dòng vào đây"></textarea>
</div>

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNo9jktuwzAMRK9CaON24XrvKgZ6gN5AG8FmGgH6ECKdJjB891D5LYec9zCb+SH6Oq9oRmN5roEEGGWlyeWQqFSBDSoeYYdjLQk6rXYuuzyXzAIJmf0fwqF1Prru02U7PDQq0CCYKHrBlsQy+Tz9rlFCDBnfdOBRqfa7twhYrhEPzvyfgmCvnxlHoIp9w76dmbbtDe+7HdpaBQUv4it6OPepLBjV8Gw5AzpjxlOJC1a9+2WB1IZQRGhWVqsdXgb1tfDcbvYbJDRqLQ==)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNo9jk2OwyAMha9isenMIpN9hok0B+gN2FjBbZEIscDpj6LcvaZpKiHg2X6f32L+mX+uM5nO2DLkwNK7RHeesoCnE85RYHEJwKPg1/f2B8gkc067AhipFDxTB4fDVlrro5ce237AKoRGjihUldjCmPqjLgkxJNoxEEqnrtp7TTEUeUT6c+Z2CUKNdgbdxZmaavt1pl+Wj3ldbcubUegumAnh2oyTp6iE95QzoDEGukzRU9Y6eg9jDcKRoFKLUm27E5RXxTu7WZ89/G4E)

</div>

Lưu ý rằng nội suy bên trong `<textarea>` sẽ không hoạt động. Thay vào đó hay sử dụng `v-model` để thay thế.

```vue-html
<!-- bad -->
<textarea>{{ text }}</textarea>

<!-- good -->
<textarea v-model="text"></textarea>
```

### Checkbox {#checkbox}

Checkbox đơn giản nhất chỉ cần một boolean value:

```vue-html
<input type="checkbox" id="checkbox" v-model="checked" />
<label for="checkbox">{{ checked }}</label>
```

<div class="demo">
  <input type="checkbox" id="checkbox-demo" v-model="checked" />
  <label for="checkbox-demo">{{ checked }}</label>
</div>

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNpVjssKgzAURH/lko3tonVfotD/yEaTKw3Ni3gjLSH/3qhUcDnDnMNk9gzhviRkD8ZnGXUgmJFS6IXTNvhIkCHiBAWm6C00ddoIJ5z0biaQL5RvVNCtmwvFhFfheLuLqqIGQhvMQLgm4tqFREDfgJ1gGz36j2Cg1TkvN+sVmn+JqnbtrjDDiAYmH09En/PxphTebqsK8PY4wMoPslBUxQ==)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNpVjtEKgzAMRX8l9Gl72Po+OmH/0ZdqI5PVNnSpOEr/fVVREEKSc0kuN4sX0X1KKB5Cfbs4EDfa40whMljsTXIMWXsAa9hcrtsOEJFT9DsBdG/sPmgfwDHhJpZl1FZLycO6AuNIzjAuxGrwlBj4R/jUYrVpw6wFDPbM020MFt0uoq2a3CycadFBH+Lpo8l5jwWlKLle1QcljwCi/AH7gFic)

</div>

Chúng ta cũng có thể ràng buộc nhiều checkbox cho cùng một mảng hoặc một [Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set) các giá trị:

<div class="composition-api">

```js
const checkedNames = ref([])
```

</div>
<div class="options-api">

```js
export default {
  data() {
    return {
      checkedNames: []
    }
  }
}
```

</div>

```vue-html
<div>Những cái tên được chọn: {{ checkedNames }}</div>

<input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
<label for="jack">Jack</label>

<input type="checkbox" id="john" value="John" v-model="checkedNames">
<label for="john">John</label>

<input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
<label for="mike">Mike</label>
```

<div class="demo">
  <div>CNhững cái tên được chọn: {{ checkedNames }}</div>

  <input type="checkbox" id="demo-jack" value="Jack" v-model="checkedNames">
  <label for="demo-jack">Jack</label>

  <input type="checkbox" id="demo-john" value="John" v-model="checkedNames">
  <label for="demo-john">John</label>

  <input type="checkbox" id="demo-mike" value="Mike" v-model="checkedNames">
  <label for="demo-mike">Mike</label>
</div>

Trong trường hợp này, mảng `checkedNames` sẽ luôn chứa các giá trị của các checkbox được chọn hiện tại.

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNqVkUtqwzAURbfy0CTtoNU8KILSWaHdQNWBIj8T1fohyybBeO+RbOc3i2e+vHvuMWggHyG89x2SLWGtijokaDF1gQunbfAxwQARaxihjt7CJlc3wgmnvGsTqAOqBqsfabGFXSm+/P69CsfovJVXckhog5EJcwJgle7558yBK+AWhuFxaRwZLbVCZ0K70CVIp4A7Qabi3h8FAV3l/C9Vk797abpy/lrim/UVmkt/Gc4HOv+EkXs0UPt4XeCFZHQ6lM4TZn9w9+YlrjFPCC/kKrPVDd6Zv5e4wjwv8ELezIxeX4qMZwHduAs=)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNqVUc1qxCAQfpXBU3tovS9WKL0V2hdoenDjLGtjVNwxbAl592rMpru3DYjO5/cnOLLXEJ6HhGzHxKmNJpBsHJ6DjwQaDypZgrFxAFqRenisM0BEStFdEEB7xLZD/al6PO3g67veT+XIW16Cr+kZEPbBKsKMAIQ2g3yrAeBqwjjeRMI0CV5kxZ0dxoVEQL8BXxo2C/f+3DAwOuMf1XZ5HpRNhX5f4FPvNdqLfgnOBK+PsGqPFg4+rgmyOAWfiaK5o9kf3XXzArc0zxZZnJuae9PhVfPHAjc01wRZnP/Ngq8/xaY/yMW74g==)

</div>

### Radio {#radio}

```vue-html
<div>Giá trị được chọn: {{ picked }}</div>

<input type="radio" id="one" value="One" v-model="picked" />
<label for="one">Một</label>

<input type="radio" id="two" value="Two" v-model="picked" />
<label for="two">Hai</label>
```

<div class="demo">
  <div>Giá trị được chọn: {{ picked }}</div>

  <input type="radio" id="one" value="One" v-model="picked" />
  <label for="one">Một</label>

  <input type="radio" id="two" value="Two" v-model="picked" />
  <label for="two">Hai</label>
</div>

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNqFkDFuwzAMRa9CaHE7tNoDxUBP0A4dtTgWDQiRJUKmHQSG7x7KhpMMAbLxk3z/g5zVD9H3NKI6KDO02RPDgDxSbaPvKWWGGTJ2sECXUw+VrFY22timODCQb8/o4FhWPqrfiNWnjUZvRmIhgrGn0DCKAjDOT/XfCh1gnnd+WYwukwJYNj7SyMBXwqNVuXE+WQXeiUgRpZyaMJaR5BX11SeHQfTmJi1dnNiE5oQBupR3shbC6LX9Posvpdyz/jf1OksOe85ayVqIR5bR9z+o5Qbc6oCk)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNqNkEEOAiEMRa/SsFEXyt7gJJ5AFy5ng1ITIgLBMmomc3eLOONSEwJ9Lf//pL3YxrjqMoq1ULdTspGa1uMjhkRg8KyzI+hbD2A06fmi1gAJKSc/EkC0pwuaNcx2Hme1OZSHLz5KTtYMhNfoNGEhUsZ2zf6j7vuPEQyDkmVSBPzJ+pgJ6Blx04qkjQ2tAGsYgkcuO+1yGXF6oeU1GHTM1Y1bsoY5fUQH55BGZcMKJd/t31l0L+WYdaj0V9Zb2bDim6XktAcxvADR+YWb)

</div>

### Select {#select}

Select đơn:

```vue-html
<div>Giá trị đã chọn: {{ selected }}</div>

<select v-model="selected">
  <option disabled value="">Vui lòng chọn một</option>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
```

<div class="demo">
  <div>Giá trị đã chọn: {{ selected }}</div>
  <select v-model="selected">
    <option disabled value="">Vui lòng chọn một</option>
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
</div>

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNp1j7EOgyAQhl/lwmI7tO4Nmti+QJOuLFTPxASBALoQ3r2H2jYOjvff939wkTXWXucJ2Y1x37rBBvAYJlsLPYzWuAARHPaQoHdmhILQQmihW6N9RhW2ATuoMnQqirPQvFw9ZKAh4GiVDEgTAPdW6hpeW+sGMf4VKVEz73Mvs8sC5stoOlSVYF9SsEVGiLFhMBq6wcu3IsUs1YREEvFUKD1udjAaebnS+27dHOT3g/yxy+nHywM08PJ3KksfXwJ2dA==)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNp1j1ELgyAUhf/KxZe2h633cEHbHxjstReXdxCYSt5iEP333XIJPQSinuN3jjqJyvvrOKAohAxN33oqa4tf73oCjR81GIKptgBakTqd4x6gRxp6uymAgAYbQl1AlkVvXhaeeMg8NbMg7LxRhKwAZPDKlvBK8WlKXTDPnFzOI7naMF46p9HcarFxtVgBRpyn1lnQbVBvwwWjMgMyycTToAr47wZnUeaR3mfL6sC/H/iPnc/vXS9gIfP0UTH/ACgWeYE=)

</div>

:::tip Lưu ý
Nếu giá trị ban đầu của biểu thức `v-model` không khớp với bất kỳ một trong các options, phần tử `<select>` sẽ render trong trạng thái "unselected". Trên iOS điều này sẽ khiến người dùng không thể chọn option đầu tiên vì iOS không kích hoạt sự kiện change trong trường hợp này. Vì vậy, chúng ta nên cung cấp một option disabled với giá trị rỗng, như được minh họa trong ví dụ trên.
:::

Đa select (ràng buộc với một mảng):

```vue-html
<div>Các giá trị đã chọn: {{ selected }}</div>

<select v-model="selected" multiple>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
```

<div class="demo">
  <div>Các giá trị đã chọn: {{ multiSelected }}</div>

  <select v-model="multiSelected" multiple>
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
</div>

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNp1kL2OwjAQhF9l5Ya74i7QBhMJeARKTIESIyz5Z5VsAsjyu7NOQEBB5xl/M7vaKNaI/0OvRSlkV7cGCTpNPVbKG4ehJYjQ6hMkOLXBwYzRmfLK18F3GbW6Jt3AKkM/+8Ov8rKYeriBBWmH9kiaFYBszFDtHpkSYnwVpCSL/JtDDE4+DH8uNNqulHiCSoDrLRm0UyWzAckEX61l8Xh9+psv/vbD563HCSxk8bY0y45u47AJ2D/HHyDm4MU0dC5hMZ/jdal8Gg8wJkS6A3nRew4=)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNp1UEEOgjAQ/MqmJz0oeMVKgj7BI3AgdI1NCjSwIIbwdxcqRA4mTbsznd2Z7CAia49diyIQsslrbSlMSuxtVRMofGStIRiSEkBllO32rgaokdq6XBBAgwZzQhVAnDpunB6++EhvncyAsLAmI2QEIJXuwvvaPAzrJBhH6U2/UxMLHQ/doagUmksiFmEioOCU2ho3krWVJV2VYSS9b7Xlr3/424bn1LMDA+n9hGbY0Hs2c4J4sU/dPl5a0TOAk+/b/rwsYO4Q4wdtRX7l)

</div>

Option có thể được render với `v-for`:

<div class="composition-api">

```js
const selected = ref('A')

const options = ref([
  { text: 'Một', value: 'A' },
  { text: 'Hai', value: 'B' },
  { text: 'Ba', value: 'C' }
])
```

</div>
<div class="options-api">

```js
export default {
  data() {
    return {
      selected: 'A',
      options: [
        { text: 'Một', value: 'A' },
        { text: 'Hai', value: 'B' },
        { text: 'Ba', value: 'C' }
      ]
    }
  }
}
```

</div>

```vue-html
<select v-model="selected">
  <option v-for="option in options" :value="option.value">
    {{ option.text }}
  </option>
</select>

<div>Selected: {{ selected }}</div>
```

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNplkMFugzAQRH9l5YtbKYU7IpFoP6CH9lb3EMGiWgLbMguthPzvXduEJMqNYUazb7yKxrlimVFUop5arx3BhDS7kzJ6dNYTrOCxhwC9tyNIjkpllGmtmWJ0wJawg2MMPclGPl9N60jzx+Z9KQPcRfhHFch3g/IAy3mYkVUjIRzu/M9fe+O/Pvo/Hm8b3jihzDdfr8s8gwewIBzdcCZkBVBnXFheRtvhcFTiwq9ECnAkQ3Okt54Dm9TmskYJqNLR3SyS3BsYct3CRYSFwGCpusx/M0qZTydKRXWnl9PHBlPFhv1lQ6jL6MZl+xoR/gFjPZTD)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNp1kMFqxCAQhl9l8JIWtsk92IVtH6CH9lZ7COssDbgqZpJdCHn3nWiUXBZE/Mdvxv93Fifv62lE0Qo5nEPv6ags3r0LBBov3WgIZmUBdEfdy2s6AwSkMdisAAY0eCbULVSn6pCrzlPv7NDCb64AzEB4J+a+LFYHmDozYuyCpfTtqJ+b21Efz6j/gPtpn8xl7C8douaNl2xKUhaEV286QlYAMgWB6e3qNJp3JXIyJSLASErFyMUFBjbZ2xxXCWijkXJZR1kmsPF5g+s1ACybWdmkarLSpKejS0VS99Pxu3wzT8jOuF026+2arKQRywOBGJfE)

</div>

## Ràng buộc Value {#value-bindings}

Đối với radio, checkbox và các option của select, giá trị được ràng buộc với `v-model` thường là các chuỗi tĩnh (hoặc boolean đối với checkbox):

```vue-html
<!-- `picked` là chuỗi có giá trị "a" khi được chọn -->
<input type="radio" v-model="picked" value="a" />

<!-- `toggle` có giá trị true hoặc false -->
<input type="checkbox" v-model="toggle" />

<!-- `selected` là một chuỗi có giá trị "abc" khi giá trị đầu tiên được chọn -->
<select v-model="selected">
  <option value="abc">ABC</option>
</select>
```

Nhưng đôi khi chúng ta muốn ràng buộc giá trị với một thuộc tính động trên instance hiện tại. Chúng ta có thể sử dụng `v-bind` để đạt được điều đó. Ngoài ra, sử dụng `v-bind` cho phép chúng ta ràng buộc giá trị input với các giá trị không phải chuỗi.

### Checkbox {#checkbox-1}

```vue-html
<input
  type="checkbox"
  v-model="toggle"
  true-value="yes"
  false-value="no" />
```

`true-value` và `false-value` là các thuộc tính chỉ định riêng cho Vue và chỉ hoạt động với `v-model`. Ở đây giá trị của `toggle` sẽ được đặt là `'yes'` khi checkbox được chọn và đặt là `'no'` khi checkbox không được chọn. Bạn cũng có thể ràng buộc chúng với các giá trị động sử dụng `v-bind`:

```vue-html
<input
  type="checkbox"
  v-model="toggle"
  :true-value="dynamicTrueValue"
  :false-value="dynamicFalseValue" />
```

:::tip Mẹo
Thuộc tính `true-value` và `false-value` không ảnh hưởng đến thuộc tính `value` của input, vì các trình duyệt không bao gồm các ô không được chọn trong các form submission. Để đảm bảo rằng một trong hai giá trị được gửi trong một form (ví dụ: "yes" hoặc "no"), hãy sử dụng radio input thay vì checkbox.
:::

### Radio {#radio-1}

```vue-html
<input type="radio" v-model="pick" :value="first" />
<input type="radio" v-model="pick" :value="second" />
```

`pick` sẽ được đặt là giá trị của `first` khi radio đầu tiên được chọn, và được đặt là giá trị của `second` khi radio thứ hai được chọn.

### Select Option {#select-options}

```vue-html
<select v-model="selected">
  <!-- inline object literal -->
  <option :value="{ number: 123 }">123</option>
</select>
```

`v-model` hỗ trợ ràng buộc giá trị của các giá trị không phải chuỗi. Trong ví dụ trên, khi option được chọn, `selected` sẽ được đặt là object literal `{ number: 123 }`.

## Modifiers {#modifiers}

### `.lazy` {#lazy}

Mặc định, `v-model` đồng bộ hóa input với dữ liệu sau mỗi sự kiện `input` (ngoại trừ IME composition, [như đã nói ở trên](#vmodel-ime-tip)). Bạn có thể thêm modifier `lazy` để đồng bộ hóa sau sự kiện `change` thay vì `input`:

```vue-html
<!-- đồng bộ hóa sau sự kiện "change" thay vì "input" -->
<input v-model.lazy="msg" />
```

### `.number` {#number}

Nếu bạn muốn đảm bảo rằng giá trị được ràng buộc là một số, bạn có thể thêm modifier `number` vào `v-model` được trong input của bạn:

```vue-html
<input v-model.number="age" />
```

Nếu giá trị không thể chuyển đổi với `parseFloat()`, giá trị ban đầu sẽ được giữ nguyên.

`number` modifier được tự động áp dụng nếu input có `type="number"`.

### `.trim` {#trim}

Nếu bạn muốn loại bỏ khoảng trắng đầu và cuối của một chuỗi một cách tự động, bạn có thể thêm modifier `trim` vào `v-model` được trong input của bạn:

```vue-html
<input v-model.trim="msg" />
```

## `v-model` với Component {#v-model-with-components}

> Nếu bạn chưa quen thuộc với component của Vue, bạn có thể bỏ qua phần này.

Các kiểu input có sẵn trong HTML đôi khi sẽ không phù hợp với nhu cầu của bạn. May mắn là, Vue component cho phép bạn tạo ra các input có thể tái sử dụng với hành vi tùy chỉnh hoàn toàn. Các input này thậm chí còn hoạt động với `v-model`! Để tìm hiểu thêm, đọc về [Usage with `v-model`](/guide/components/v-model) trong hướng dẫn về Components.
