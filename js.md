# js 相关问题

## 定时器

Q: 下面代码的输出是什么，说明原因。

```js
    var timerID = setTimeout(function () {
        console.log('second');
    }, 10);

    clearInterval(timerID);
    console.log('first');
```

A: 输出：

first

原因：所有创建的定时器任务都在同一个列表中，这个列表被称为 `活动的定时器列表`。该列表中的实体有一个唯一的数字来标识。`clearTimeout` 与 `clearInterval` 清除的是这个列表中的实体，因为实体的 ID 是唯一的，所以用哪个方法清除都是可以的。参考 [whatwg 定时器规范][whatwg timers]。


Q: 定时器执行的最小间隔是多少？请详细说明各种情况下的最小间隔。

A: [whatwg 定时器规范][whatwg timers] 没有规定执行的最小间隔，通常情况下，可以认为最小间隔为 1 毫秒（chrome 57 实测）。然后，当定时器嵌套的层数大于等于 5 层的时候，最小间隔并设定为 4 毫秒。下面的代码及输出验证了这一点。

```js
var t0 = performance.now();
setTimeout(function () {
    var t1 = performance.now();
    console.log('timer 1', t1 - t0)

    setTimeout(function () {
        var t2 = performance.now();
        console.log('timer 2', t2 - t1)

        setTimeout(function () {
            var t3 = performance.now();
            console.log('timer 3', t3 - t2)

            setTimeout(function () {
                var t4 = performance.now();
                console.log('timer 4', t4 - t3)

                setTimeout(function () {
                    var t5 = performance.now();
                    console.log('timer 5', t5 - t4)

                    setTimeout(function () {
                        var t6 = performance.now();
                        console.log('timer 6', t6 - t5)
                        
                        setTimeout(function () {
                            var t7 = performance.now();
                            console.log('timer 7', t7 - t6)
                        }, 0);
                    }, 0);
                }, 0);
            }, 0);
        }, 0);
    }, 0);
}, 0);

// Output in Chrome 57
// timer 1 1.5499999999883585
// timer 2 2.095000000030268
// timer 3 1.8999999999650754
// timer 4 1.820000000006985
// timer 5 4.8250000000116415
// timer 6 4.7049999999580905
// timer 7 5.5450000000419095
```


Q: 讲一下 Base64 编码的原理，并将下面的字符串使用 base64 加密。

```js
var str = `{"name":"wx","age":24}`;
```

A: 3 个 byte 转换为 4 个 byte。原 3 个 byte 顺序排列为 24 bit，然后每 6 个 bit 一组，组成 4 组。每组前面补两个 0 bit，凑成 1 个 byte，即可得到转化后的 4 个 byte。由于是 3 个 byte 转换为 4 个 byte，如果字符串结尾只有一个或两个字符，那么转换后用 `=` 填充。编码字符包括 A-Za-z0-9+/共 64 个 字符，依次代表 0 - 63。

str 使用 base64 加密的结果：

```js
self.btoa(str);
```

参考 [Base64 工具方法][whatwg base64]。


Q: 已知字符 A 的 ASCII 码为 65，根据 `str1` 的 Base64 解码结果，对字符串 `str2`、`str3`、`str4`、`str5` 和 `str6` 进行 Base64 解码。

```js
var str1 = "YQ==";
// str1 Base64 解码结果："a"
var str2 = "YQ=";
var str3 = "YQ";
var str4 = "Y Q";
var str5 = "YR";
var str6 = "Y"
```

A: `str2` 和 `str6` 是错误的 Base64 编码，故解码报错。`str3` 和 `str4` 的解码结果都是 "a"，她们与 `str1` 属于相同的编码结果，且解码的过程会忽略数据中的 ASCII 码空格。`str5` 在解码的时候会丢掉低 2 bit，而 'Q' 和 'R' ASCII 码只差 1，高 6 位相同，故而她们的解码值也相同。参考 [Base64 工具方法][whatwg base64]。


Q:  如何计算一个页面打开的时间？

A: performance.now()



<!-- Links -->
[whatwg timers]: https://html.spec.whatwg.org/multipage/webappapis.html#timers
[whatwg base64]: https://html.spec.whatwg.org/multipage/webappapis.html#atob