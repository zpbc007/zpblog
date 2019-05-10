---
title: Promise实现
date: 2019-05-06 09:16:41
tags:
    - promise
keyword:
    - promise
    - typescript
categories:
    - javascript
description: 通过 typescript 实现 promise 来了解 promise 的运行过程
---

# 通过 typescript 实现 promise 来了解 promise 的运行过程

本文使用 typescript 一步步实现 Promise 来了解 Promise 的原理。Promise 的具体用法可参考[Promise 对象](http://es6.ruanyifeng.com/#docs/promise)

## `Promise`基本用法

```typescript
new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve('异步操作成功')
    }, 1000)
})
```

`Promise` 构造函数接受一个函数作为参数，该函数的两个参数分别是 `resolve` 和 `reject`。因此我们需要在构造函数中判断传入的是否为一个function。

```typescript
const isFunction = (func) => typeof func === 'function'
```

接下来定义我们自己的 `Promise` 叫做 `ZPromise`

```typescript
type IHandleFunc = (resolve: IResolve, reject: IReject) => void

/** T 为 promise resolve 时返回的值的类型 */
class ZPromise<T = any> {
    constructor(handleFunc: IHandleFunc) {
        if (!isFunction(handleFunc)) {
            throw new Error('ZPromise need function as handler')
        }
    }
}
```

## `Promise` 的状态

`Promise` 共有三种状态

- Pending(进行中)
- Fulfilled(已成功)
- Rejected(以失败)

在 `Promise` 初始化时状态为 Pending, 当调用 resolve 时状态转为 Fulfilled, 当调用 reject 时状态转为 Rejected，并且当 `Promise` 状态变为 Fulfilled 或 Rejected 后，`Promise` 的状态不会再发生改变。

接下来在我们的`ZPromise`上添加此逻辑

```typescript
type IResolve<T> = (value?: T | ZPromise<T>) => void
type IReject = (reason?: any) => void
type IHandleFunc = (resolve: IResolve, reject: IReject) => void

/** promise的状态 */
enum PromiseState {
    PENDING,
    FULFILLED,
    REJECTED,
}

/** T 为 promise resolve 时返回的值的类型 */
class ZPromise<T = any> {
    /** 初始状态为pending */
    private state = PromiseState.PENDING
    constructor(handleFunc: IHandleFunc) {
        if (!isFunction(handleFunc)) {
            throw new Error('ZPromise need function as handler')
        }

        try {
            handleFunc(this.resolve, this.reject)
        } catch (err) {
            this.reject(err)
        }
    }

    private readonly isPending = () => this.state === PromiseState.PENDING

    private readonly resolve: IResolve<T> = (value) => {
        /** resolve 只能从 pending 转换为 fulfilled */
        if (!this.isPending()) {
            return
        }

        // 转换状态
        this.state = PromiseState.FULFILLED
        this.val = value
    }

    private readonly reject: IReject = (error) => {
        /** reject 只能从 pending 转换为 rejected */
        if (!this.isPending()) {
            return
        }

        this.state = PromiseState.REJECTED
        this.val = error
    }
}
```

## `Promise` 的 `then` 方法

- 参数

    `Promise` 的 `then` 方法接收两个函数作为参数。第一个函数在 `Promise` 状态变为 `FULFILLED` 时被调用，第二个参数在 `Promise` 状态变为 `REJECTED` 时被调用。

- 返回值

    在同一个 `Promise` 对象上可以多次调用 `then` 方法，且每个 `then` 方法的返回值均为一个新的 `Promise` 对象

- resolve \ reject 的值

    如果 `resolve`、`reject` 的值不为 `Promise` 则直接将这个值用 `Promise` 包裹，传入回调函数中。 如果是 `Promise` 则需等待值得状态变为非 `PENDING` 再去调用回调函数


基于以上描述的实现

```typescript
type IResolve<T> = (value?: T | ZPromise<T>) => void
type IReject = (reason?: any) => void
type IHandleFunc = (resolve: IResolve, reject: IReject) => void

/** promise的状态 */
enum PromiseState {
    PENDING,
    FULFILLED,
    REJECTED,
}

/** T 为 promise resolve 时返回的值的类型 */
class ZPromise<T = any> {
    /** 初始状态为pending */
    private state = PromiseState.PENDING
    constructor(handleFunc: IHandleFunc) {
        if (!isFunction(handleFunc)) {
            throw new Error('ZPromise need function as handler')
        }

        try {
            handleFunc(this.resolve, this.reject)
        } catch (err) {
            this.reject(err)
        }
    }

    then<TResult = T>(onFulfilled?: IOnFulfilled<T, TResult>, onRejected?: IOnRejected<TResult>) {
        return new ZPromise<TResult>((resolve, reject) => {
            const fulfilled = (value: T) => {
                try {
                    // 未传入 onFulfilled 直接进入fulfilled状态
                    if (!isFunction(onFulfilled)) {
                        resolve(value as any)
                    } else {
                        // 得到上个promise的返回值
                        const res = onFulfilled(value)

                        // 返回的为一个新的 promise, 等待它变为fulfilled 状态
                        if (res instanceof ZPromise) {
                            res.then(resolve, reject)
                        } else {
                            // 返回的为一个普通值
                            resolve(res)
                        }
                    }
                } catch (e) {
                    // 期间发生错误
                    reject(e)
                }
            }

            const rejected = (error: any) => {
                try {
                    // 上一个 promise 未传入 onRejected 进入当前的 rejected 状态
                    if (!isFunction(onRejected)) {
                        reject(error)
                    } else {
                        // 上一个 catch 的返回值
                        const res = onRejected(error)

                        // 返回的为一个新的 promise 等待它变为 fulfilled 状态
                        if (res instanceof ZPromise) {
                            res.then(resolve, reject)
                        } else {
                            // 返回的为普通值
                            resolve(res)
                        }
                    }
                } catch (e) {
                    // 期间发生错误
                    reject(e)
                }
            }

            switch (this.state) {
                // pending 状态加入队列
                case PromiseState.PENDING:
                    this.fulfilledQueue.push(fulfilled)
                    this.rejectedQueue.push(rejected)
                    break
                case PromiseState.FULFILLED:
                    fulfilled(this.val)
                    break
                case PromiseState.REJECTED:
                    rejected(this.val)
                    break
            }
        })
    }

    private readonly isPending = () => this.state === PromiseState.PENDING

    private readonly resolve: IResolve<T> = (value) => {
        /** resolve 只能从 pending 转换为 fulfilled */
        if (!this.isPending()) {
            return
        }

        // 转换状态
        this.state = PromiseState.FULFILLED
        this.val = value
    }

    private readonly reject: IReject = (error) => {
        /** reject 只能从 pending 转换为 rejected */
        if (!this.isPending()) {
            return
        }

        this.state = PromiseState.REJECTED
        this.val = error
    }
}
```

## `Promise` 的 `catch` 方法

`catch` 方法其实就是 `then` 方法但没有第一个参数

```typescript
catch(onRejected) {
    return this.then(null, onRejected)
}
```

## 静态 `resolve` 方法

```typescript
static resolve = (value) => {
    if (value instanceof ZPromise) {
        return value
    }

    return new ZPromise((resolve) => {
        resolve(value)
    })
}
```

## 静态 `reject` 方法

```typescript
static reject = (error) => {
    return new ZPromise((_, reject) => {
        reject(error)
    })
}
```

## 静态 `all` 方法

```typescript
static all = (proList: ZPromise[]) => {
    return new ZPromise((resolve, reject) => {
        const values = []
        let count = 0

        for (const [index, pro] of proList.entries()) {
            pro.then(
                (res) => {
                    values[index] = res
                    count++

                    // 所有的都 resolve 后才会 resolve
                    if (count === proList.length) {
                        resolve(values)
                    }
                },
                (error) => {
                    // 如果有一个为 error 则直接reject
                    reject(error)
                }
            )
        }
    })
}
```

## 静态 `race` 方法

```typescript
static race = (proList: ZPromise[]) => {
    return new ZPromise((resolve, reject) => {
        for (const pro of proList) {
            pro.then(
                (res) => {
                    resolve(res)
                },
                (error) => {
                    reject(error)
                }
            )
        }
    })
}
```

