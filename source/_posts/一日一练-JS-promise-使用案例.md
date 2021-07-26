---
title: 一日一练-JS promise 使用案例
date: 2021-07-22 15:19:51
tags:
  - 技术
  - 一日一练
  - promise
categories: 技术
---


下面的案例 1 、 案例 3 是平常工作中常常遇到，案例 2 则是对 `Promise.any` 的自己实现

1. 对于耗时较长的计算，比如有一个非常长的数组要对其项进行相加，此时导致页面出现卡顿，如何解决这个问题。
2. 实现一个对所有 promise 失败，则走 catch，有一个成功就走 then。是Promise.all() 的相反实现。
3. a b c 三个请求，当三个请求都成功后请求 d 。若有失败则重新请求失败的请求，最多重试三次重新请求成功后请求 d；三次都失败则停止请求。

<!--more-->

## 案例1
对于耗时较长的计算，比如有一个非常长的数组要对其项进行相加，此时导致页面出现卡顿，如何解决这个问题。

```js
    function calcLong(arr) {
        return new Promise((resolve, reject) => {
            console.log(arr)
            let result = 0
            const total = arr.length
            const once = 100

            const loopCount = Math.ceil(total / 100)
            let countOfLoop = 0 // 计数次数

            function chunk() {
                console.log('chunk:' + countOfLoop);
                for (var i = 0; i < once && i < arr.length; i++) {
                    result += arr[i];
                }
                arr.splice(0, once)
                console.log('arr', arr);
                countOfLoop++
                loop()
            }

            function loop() {
                console.log('loop');
                console.log('loop:', 'countOfLoop:' + countOfLoop, ';loopCount：' + loopCount)
                if (countOfLoop < loopCount - 1) {
                    requestAnimationFrame(chunk)
                }  else {
                    resolve(result)
                }
            }
            chunk()
        })

    }

    let f = length => Array.from({ length }).map((v, k) => k);
    let arr = f(1000)
    calcLong(arr).then(val => {
        console.log('calcLong...')
        console.log(val)
    }).catch(err => {
        console.log('err:' + err);
    })
```

## 案例2
实现一个对所有 promise 失败，则走 catch，有一个成功就走 then。是Promise.all() 的相反实现。

```js
Promise.myMethod = function (promises) {
  return new Promise((resolve, reject) => {
    let len = promises.length
    promises.forEach(promise => {
      promise.then(value => {
        resolve(value)
      }).catch(err => {
        len--;
        if (len == 0) {
          reject(err)
        }
      })
    })
  })
}

Promise.myMethod([Promise.reject('Error A'), Promise.reject('Error B'), Promise.reject('Error C')]).then(val => {
  // 成功
  console.log(val)
}).catch(errs => {
  console.log('失败')
  console.log(errs)
})

Promise.myMethod([Promise.reject('Error A'), Promise.reject('Error B'), Promise.resolve('Success C')]).then(val => {
  // 成功
  console.log(val)
}).catch(errs => {
  console.log('失败')
  console.log(errs)
})
```

## 案例3
a b c 三个请求，当三个请求都成功后请求 d 。若有失败则重新请求失败的请求，最多重试三次重新请求成功后请求 d；三次都失败则停止请求。
```js
function tryRequest(requests) {
  let len = requests.length;
  let retry = 0; // 小于 3 次
  return new Promise((resolve, reject) => {
    requests.forEach(request => {
      (function exeSelf(request) {
        request.then(val => {
          len--
          if (len == 0) {
            resolve()
          }

        }).catch(err => {
          retry++
          console.log('try...', retry)
          if (retry < 3) {
            exeSelf(request)
          } else if (retry == 3) {
            reject('尝试了3次')
          }
        })
      })(request)
    })
  })
}

tryRequest([Promise.resolve('Suc A'), Promise.resolve('Suc B'), Promise.resolve('Suc C')])
  .then(() => {
    Promise.resolve('Suc D').then(val => {
      console.log(val)
    })
  })
  .catch(err => {
    console.log('err:')
  })

tryRequest([Promise.resolve('Suc A'), Promise.resolve('Suc B'), Promise.reject('Err C')])
  .then(() => {
    Promise.resolve('Suc D').then(val => {
      console.log(val)
    })
  })
  .catch(err => {
    console.log('err:', err)
  })

```