# Typescript

### 一些比較特殊不常見的型別

<br>

> 目前已經有相當大一部分的第三方module都已經使用Typescript  
> 所以有很多繼承自module的props都可以在source code中直接找到它的型別  
> 或是直接將component的型別import進程式碼的作法

- extends型別
```js
import { IconProps as defaultIconProps } from '@material-ui/core'
...
export interface IconProps extends defaultIconProps {
  ...
}
```

- `style` for styled-components
```js
import { CSSProperties } from 'react'
...
style?: CSSProperties
```

- `children` & `element`
- `ReactNode`, `ReactElement`, `ReactChildren`
  - 我的理解是要根據不同的props需要去選擇上述的型別
  - `ReactElement`基本上只包含type、props、key三個屬性，在React中幾乎和`JSX.Element`等價
  - `ReactNode`則是一個聯合型別，當中包含`ReactElement`
  - `ReactChildren`比較少見，但我尚未釐清它的定義就不贅述

<br>

- React ref
```js
import { LegacyRef } from 'react'
...
refs?: LegacyRef<Element>
```

- React event
  - `FocusEvent`, `KeyboardEvent`, `MouseEvent`, `SyntheticEvent` ...
  - 根據需求選擇

<br>

- `setTimeout()` & `setInterval()`
```js
let timer: NodeJS.Timeout
```

- `response` from `Promise`
```js
const tplResponse = new Response()
...
response: typeof tplResponse
```

<br>

在學習Typescript的過程中曾經聽到一個觀念:
> 在使用Typescript時要盡可能避免`any`型別的出現，否則使用Typescript是毫無意義的

但是在專案中其實存在很大量的物件，它的資料結構可能非常複雜  
或是它是透過fetch http request得來的資料，有的時候並沒有辦法完全的剖析它的資料型別和結構  
但為了避免使用`any`我可能會定義一個全域的型別去賦予這樣的物件。
```js
export interface Prop {
  [key: string]: any
}
```

<br>
