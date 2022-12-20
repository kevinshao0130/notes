# CSS 

- 透過`background-image`客製化Border  
[Customize CSS Border](https://kovart.github.io/dashed-border-generator/)

<br>

# Next.js

## [Dynamic import](https://nextjs.org/docs/advanced-features/dynamic-import)

某些第三方module不支援Server-Side-Rendering，包版過後會在server端出現錯誤

### styled component
```js
const Module = dynamic(() => import('module').then(module => module.Module), { ssr: false })
...
<Module>
...
</Module>
```

### function
ex: [LINE Front-end Framework (LIFF)](https://reurl.cc/qZallN)
```js
const liff = (await import('@line/liff')).default
await liff.ready
```

### hook或其他props
[source](https://stackoverflow.com/questions/63126355/loading-react-hooks-using-dynamic-imports/63438354#63438354)

1. 自定義hook `useSuspense.js`
```js
const cache = {}
const errorsCache = {}

export default function useSuspense (importPromise, cacheKey) {
  const cachedModule = cache[cacheKey]
  if (cachedModule) {
    return cachedModule
  }

  if (errorsCache[cacheKey]) {
    throw errorsCache[cacheKey]
  }

  throw importPromise
    .then((mod) => (cache[cacheKey] = mod))
    .catch((err) => {
      errorsCache[cacheKey] = err
    })
}

```

2. 使用這個hook將要import的hook或其他props宣告進來
```js
import useSuspense from 'useSuspense'

export default function Module (props) {
  const { useModule } = useSuspense(import('module'), 'module')
  const module = useModule()
  ...
}

```

3. 需要在parent包一個`<Suspense>`
```js
import { Suspense } from 'react'
...
<Suspense fallback={null}>
  <Module>
  ...
  </Module>
</Suspense>
```

<br>

# LocalStorage & Cookie

|  | Cookie | LocalStorage |
| :------: | :------: | :------: |
| 失效時間 | 關閉瀏覽器後 | - |
| 大小限制 | 4kb | 5mb |

### browser的儲存空間在無痕模式下有很大的限制(以Chrome為例)

一般模式:  
![browser_storage.png](/files/browser_storage.png)  
無痕模式:  
![browser_storage_private.png](/files/browser_storage_private.png)

<br>

# node & npm

## node

### node 16
在npm更新到v7(含)以上之後，會預設安裝`peerDependencies`，而node從14 -> 16時，npm也從6 -> 8。
專案內使用的不同套件依賴的`peerDependencies`不同時就會安裝錯誤  
解決方式:
```
npm install --legacy-peer-deps
```
[參考](https://blog.poychang.net/npm-install-with-legacy-peer-deps/)

### node 18
新支援了openssl 3.0，在start或build專案時可能會遇到[error:0308010C](https://bobbyhadz.com/blog/react-error-digital-envelope-routines-unsupported)  
需要在start或build的script上加上:
```
NODE_OPTIONS=--openssl-legacy-provider
```

### 關於npm install
[參考](https://stackoverflow.com/a/53311374)  
當執行`npm install`時，npm會去安裝套件，然後會再回去執行`package.json`中`install`的script  
所以我們不能用以下方式改寫`npm install`
```json
"scripts": {
    "install": "npm install --legacy-peer-deps"
  }
```
這樣會造成npm無限輪迴去執行`install`這個指令

<br>

# Gulp Task runner

### 執行順序的問題
 
gulp中每個task的callback func(通常叫做`done()`)，並不會等待上面的部分執行完才會觸發，算是為了告訴gulp可以執行下一個task了。[參考](https://stackoverflow.com/a/29695977)   

> 那要如何確保task可以有async/await的效果呢

1. [解法1](https://stackoverflow.com/a/70886817)，直接把task當作promise整個return回去，而不是使用`done()`。

2. [解法2](https://stackoverflow.com/a/57366865)，`on('end')`

```js
// 解法1
gulp.task('task', () => {
  return doSomething()
})

// 解法2
gulp.task('task', done => {
  doSomething()
  .on('end', done)
})

// instead of this
gulp.task('task', done => {
  doSomething()
  done()
})
```


<br>

# webpack

<br>
