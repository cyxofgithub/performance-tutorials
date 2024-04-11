# [使用 requsetIdleCallback](https://developer.chrome.com/blog/using-requestidlecallback?hl=en)

## 为什么我们要使用 requestIdleCallback？

自行安排非必要的工作非常困难。因为在 requestAnimationFrame 回调执行后，还有样式计算、布局、绘制和其他浏览器内部程序需要运行，所以不可能准确计算出还剩下多少帧时间。自滚动解决方案无法考虑这些因素。为了确保用户没有以某种方式进行交互，你还需要为每一种交互事件（滚动、触摸、点击）附加监听器，即使你不需要它们来实现功能，这样你才能绝对确保用户没有进行交互。另一方面，浏览器清楚地知道帧末有多少时间可用，以及用户是否在进行交互，因此通过 requestIdleCallback，我们获得了一个 API，可以最有效地利用任何空闲时间。

让我们更详细地了解一下它，看看如何利用它。

## 检查 requestIdleCallback

requestIdleCallback 还处于早期阶段，因此在使用前应检查它是否可用：

```javascript
if ("requestIdleCallback" in window) {
    // Use requestIdleCallback to schedule work.
} else {
    // Do what you’d do today.
}
```

## 使用

调用 requestIdleCallback 与 requestAnimationFrame 非常相似，都是将回调函数作为第一个参数：

```javascript
requestIdleCallback(myNonEssentialWork);
```

调用 myNonEssentialWork 时，会得到一个 deadline 对象，该对象包含一个函数，该函数返回一个数字，表示你的工作还剩多少时间：

```javascript
function myNonEssentialWork(deadline) {
    while (deadline.timeRemaining() > 0) doWorkIfNeeded();
}
```

可以调用 timeRemaining 函数来获取最新值。当 timeRemaining() 返回零时，如果还有更多工作要做，就可以安排另一个 requestIdleCallback：

```javascript
function myNonEssentialWork(deadline) {
    while (deadline.timeRemaining() > 0 && tasks.length > 0) doWorkIfNeeded();

    if (tasks.length > 0) requestIdleCallback(myNonEssentialWork);
}
```

## 确保您的函数被调用

...待续
