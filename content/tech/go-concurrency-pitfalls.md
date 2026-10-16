---
title: "Go 并发模式：我踩过的三个坑"
date: 2026-09-26
tags: ["Go", "并发", "技术"]
summary: "channel 泄漏、context 取消、goroutine 逃逸——聊聊真实项目里翻过的车。"
---

Go 的并发原语看起来很简单：`go` 关键字一加，channel 一传，好像就完事了。但线上跑起来，问题往往出在那些「看起来没问题」的地方。

## 坑一：channel 没人接收，goroutine 永久阻塞

最经典的一个。写日志上报的时候，我开了个 goroutine 往 channel 里写，但下游消费挂了：

```go
func report(logs []string) {
    ch := make(chan string)
    go func() {
        for _, l := range logs {
            ch <- l        // 下游没人收，这里永久阻塞
        }
        close(ch)
    }()
    // 忘了 range ch
}
```

这个 goroutine 会一直挂在那里，占着内存，永远不会被回收。请求量一上来，内存就缓慢上涨。

**修正**：要么保证有消费者，要么给 channel 加缓冲并配合 `select` + `ctx.Done()` 做超时退出。

```go
select {
case ch <- l:
case <-ctx.Done():
    return
}
```

## 坑二：context 传了，但没有真正取消

`context.WithTimeout` 用得很顺手，但如果下游函数根本没监听 `ctx.Done()`，这个超时形同虚设。

```go
ctx, cancel := context.WithTimeout(context.Background(), 3*time.Second)
defer cancel()

// 这个函数内部如果完全无视 ctx，超时不会生效
result, err := slowQuery(ctx, sql)
```

检查清单里应该有一条：**每个接收 ctx 的函数，都必须有监听它的地方**。

## 坑三：循环变量捕获

Go 1.22 之前，这个 bug 几乎人人都写过：

```go
for _, item := range items {
    go func() {
        process(item)   // 全部拿到最后一个 item
    }()
}
```

Go 1.22 起循环变量改为每轮独立，这个问题自然消失了。但如果你维护的是老代码，或者循环里用的是别的变量，还是要留个心眼。

## 我现在会做的三件事

1. 每个 goroutine 都要能说清楚它**什么时候退出**
2. 每个 `ctx` 都要能找到**监听它的那一行**
3. `go vet` 和 race detector 加入 CI，不通过不让合并

前两条是设计纪律，第三条是机械保障。缺一不可。
