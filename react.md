# React

## Lists and Keys
如果Array內包含資料的primary key(或被稱為id)，請使用primary key作為每一個item的key，如果沒有primary key才可以直接使用Array的index
React官方並不建議使用index作為key，source: [React Lists and Keys](https://reactjs.org/docs/lists-and-keys.html#keys)

<br>

## React 18 new api

### `useEffect()`
- 在React 18中會執行2次  
> In the future, we’d like to add a feature that allows React to add and remove sections of the UI while preserving state.  
...  
To help surface these issues, React 18 introduces a new development-only check to Strict Mode. This new check will automatically unmount and remount every component, whenever a component mounts for the first time, restoring the previous state on the second mount.

[source](https://reactjs.org/docs/strict-mode.html#ensuring-reusable-state)

解法:
1. 移除`<React.StrictMode />`
2. customize `useEffect()`
```js
import { useRef, useState, useEffect } from 'react'

export const useOnceEffect = (effect: () => void | (() => void)) => {
  const destroyFunc = useRef<void |(() => void)>()
  const effectCalled = useRef(false)
  const renderAfterCalled = useRef(false)
  const [, setVal] = useState<number>(0)

  if (effectCalled.current) {
    renderAfterCalled.current = true
  }

  useEffect(() => {
    if (!effectCalled.current) {
      destroyFunc.current = effect()
      effectCalled.current = true
    }

    setVal((val) => val + 1)

    return () => {
      if (!renderAfterCalled.current) {
        return
      }
      if (destroyFunc.current) {
        destroyFunc.current()
      }
    }
  }, [])
}

```
### `startTransition()`
[參考1](https://milkmidi.medium.com/react-18-starttransition-1d71275d17dd)
[參考2](https://juejin.cn/post/7029120932086022174)
- 把不急著更新(non-urgent)但是會觸發畫面重新的function包在裡面，讓畫面可以優先更新需要立即反應的部分 

### `useTransition()`
[參考3](https://ithelp.ithome.com.tw/articles/10281124)
- `startTransition()`執行時回傳一個boolean值，可以用來做ui顯示的判斷

### `useDefferedValue()`
- 畫面重新render需要更多時間，所以設定一個延遲的timeout內先顯示舊的value，避免畫面過度頻繁的更新

<br>

## redux-thunk

> redux-thunk的目的就是為了實踐middleware，讓action能做更多的事 [source](https://ithelp.ithome.com.tw/articles/10240464)

透過middleware我們就能透過`dispatch()` 去包裝多種不同功能的function

[redux-thunk](https://github.com/reduxjs/redux-thunk) 簡化後的source code
```js
function createThunkMiddleware(extraArgument) {
  return ({ dispatch, getState }) => next => action => {
    if (typeof action === 'function') { // 如果傳入的action為function
      // 則將dispatch, getState, extraArgument一起回傳後執行function
      return action(dispatch, getState, extraArgument);
    }

    return next(action); // 否則回傳action給下一個middleware
  };
}
```

<br>

## react-router v6

[官方文件](https://reactrouter.com/en/v6.3.0/getting-started/overview)
[參考文件](https://ithelp.ithome.com.tw/articles/10282773)

### 巢狀路由
[Nested Routes](https://reactrouter.com/en/v6.3.0/getting-started/overview#nested-routes)

```js
<Route path='parent' element={<Parent />}>
  <Route path='children' element={<Children />} />
</Route>
```

需要在parent Route的頁面加上`<Outlet />`，宣告需要render children的位置

```js
import { Outlet } from 'react-router-dom'
...
export default function Parent () {
  return (
    <div>
      ...
      <Outlet />
    </div>
  )
}
```

- 這裡有發現一個小bug: 當children內沒有state或是reducer變數觸發re-render時，parent頁面也可能不會觸發re-render，而是只會渲染初始化的狀態。

<br>