# ECMAScript 规范相关问题

<!-- TOC -->

- [ECMAScript 规范相关问题](#ecmascript-规范相关问题)
  - [闭包](#闭包)
  - [Unicode 编码](#unicode-编码)
  - [Object](#object)
  - [杂](#杂)

<!-- /TOC -->

## 闭包

Q: 下面几种情况的 js 代码输出是什么？

```js
// 情况一
const obj = {
  age: 23,
  sayAge: function () {
    console.log('age', this.age);
  }
}

obj.sayAge();
const { sayAge } = obj;
sayAge();
```

```js
// 情况二
const obj = {
  [this]: 'a',
  hello: () => {
    console.log('this', this['this']);
  }
}
console.log(Object.keys(obj));
obj.hello();
```

```js
// 情况三
const obj = {
  hi: function () {
    const f1 = function () {
      'use strict';
      console.log('this', this);
    }

    const f2 = f1.bind(this);
    f2();
  }
}

obj.hi();
const { hi } = obj;
hi();
```

```js
// 情况四
const obj = {
  hi: function () {
    const f1 = () => {
      console.log('this', this);

      function f2() {
        console.log('this', this);
      }

      f2();
    }

    f1();
  }
}

obj.hi();
const { hi } = obj;
hi();
```

A: 输出如下

```
// 情况一
23
undefined
```

```
// 情况二
['hello', '[object Window]']
undefined
```

```
// 情况三
obj
window or global
```

```
// 情况四
obj, window or global
window or global, window or global
```

## Unicode 编码

Q: js 代码使用什么编码来表示的，能表示多少种字符。

A: ECMAScript 使用 Unicode 编码字符集，ES 5 使用 Unicode 3.0+, ES6 使用 Unicode 5.1+。Unicode 字符集中字符所在的位置称作码位，码位值范围 U+0000 ~ U+10FFFF。源码文本是由码位组成的序列。ECMAScript 源码文本使用 UTF-16 进行字符编码，16位为一个码元。参考 [码元][code unit]、[码位][code point]、[ECMA-262 5.1 规范之 Source Text][ecma-262 5.1 source text]、[百度百科 Unicode][baike unicode]。


Q: 描述 String 的方法 charCodeAt 与 codePointAt 的区别，以及 String.fromCharCode 与 String.fromCodePoint 的不同。

A: ECMAScript 使用 UTF-16 为字符编码，码元为 16-bits，Unicode 字符表示为 1~2 个码元。charCodeAt 返回 UTF-16 编码字符的1个码元的值，codePointAt 返回  UTF-16 编码字符的码位值。如果当前索引是第一个码元且与第二个码元能组成有效的编码，则返回码位值。如果不能组成有效的编码或者索引是第二个码元，则直接返回码元的值。BMP 内的字符，二者值一样，BMP 以外的字符，charCodeAt 的返回值位于代理区。String.fromCharCode 与 String.fromCodePoint 类似。参考 [MDN charCodeAt][mdn charCodeAt]、[ECMA-262 2015 规范之 charCodeAt][ecma-262 2015 charCodeAt]、[ECMA-262 2015 规范之 codePointAt][ecma-262 2015 codePointAt]、[WIKI UTF-16][wiki UTF-16]、[ECMA-262 2015 规范之 fromCharCode][ecma-262 2015 fromCharCode]。


Q: 将字符串 `'中国'`转换为 utf-8 编码

A: 两种方法，一、利用已有的工具方法；二、利用编码原理编写工具方法。参考 [btoa 编码 Unicode 字符串 ][mdn btoa]、[escape][escape]、[百度百科 Unicode][baike unicode]、[Unicode 与 UTF-8 的区别][unicode & utf-8]。

```js
// 方法一
function utf16ToUTF8(str) {
    return unescape(encodeURIComponent(str));
}

function utf8ToUTF16(str) {
    return decodeURIComponent(escape(str));
}

utf16ToUTF8('中国');
```
注：js 引擎内，encodeURIComponent(str) 相当于 escape(utf16ToUTF8(str))，详见 [encodeURIComponent 定义][encodeURIComponent definition]

```js
// 方法二
function unicodeToUTF8(cp) {
    if (Number.isNaN(cp)) {
        return NaN;
    }
    if (typeof cp !== 'number' || !Number.isInteger(cp) || cp < 0 || cp > 0x7FFFFFFF) {
        throw TypeError('parameter cp is not a valid cp point');
    }
    if (cp <= 0x7F) {
        return String.fromCodePoint(cp);
    }
    const merge = (h, l) => (h << 8) + l;
    const byteArr = [(cp & 0x3F) + 0x80];

    switch (true) {
        case cp <= 0x7FF:
            byteArr.unshift(
                (cp >> 6 & 0x1F) + 0xC0
            );
            break;
        case cp <= 0xFFFF:
            byteArr.unshift(
                (cp >> 12 & 0xF) + 0xE0,
                (cp >> 6 & 0x3F) + 0x80
            );
            break;
        case cp <= 0x10FFFF:
            byteArr.unshift(
                (cp >> 18 & 0x7) + 0xF0,
                (cp >> 12 & 0x3F) + 0x80,
                (cp >> 6 & 0x3F) + 0x80
            );
            break;
        // 目前 Unicode 只定义了 17 个平面，不会超过 0x10FFFF，
        // 后面的代码可去掉，这部分是对 UCS-4 的整体实现。
        case cp <= 0x3FFFFFF:
            byteArr.unshift(
                (cp >> 24 & 0x3) + 0xF8,
                (cp >> 18 & 0x3F) + 0x80,
                (cp >> 12 & 0x3F) + 0x80,
                (cp >> 6 & 0x3F) + 0x80
            );
            break;
        default:
            byteArr.push(
                (cp >> 30 & 0x1) + 0xFC,
                (cp >> 24 & 0x3F) + 0x80,
                (cp >> 18 & 0x3F) + 0x80,
                (cp >> 12 & 0x3F) + 0x80,
                (cp >> 6 & 0x3F) + 0x80
            );

    }
    return byteArr.map(x => String.fromCodePoint(x)).join('');
}

'中国'.split('').map(x => x.codePointAt(0)).map(unicodeToUTF8).join('');
```

Base64 编码问题可以查看 [HTML 规范相关问题][html spec] 相关部分内容。


## Object

Q: 实现 Object.is 方法，逻辑是 +0 和 -0 不等，NaN 和 NaN 相等，其他类型变量按照类型严格相等。

A: 代码如下：
```js
function equal(a, b) {
    if (a === b) {
        return a !== 0 || 1 / a === 1 / b;
    }
    return a !== a && b !== b;
}
```

参考 [MDN Object.is][mdn Object.is]、[ES 6 Object.is][ecma-262 2015 Object.is]。

## 杂

Q: 如何证明 for ( let i = 0; i < arr.length; i++ ) 中每次循环都会获取 arr.length 属性

A: 方法有很多种，这里列举两种。

方法一：
```js
const obj = {
  0: 'a',
  1: 'b',
  2: 'c',
  3: '4'
};

Object.defineProperty(obj, 'length', {
  get: function () {
    console.log('get length');
    return 4;
  }
})

for(let i =0; i < obj.length; i++) {
  console.log(`key: ${i}, value: ${obj[i]}`);
}

// 输出中会出现 get length 4 次
```

方法二：
```js
const arr = ['a', 'b'];

for (let i = 0; i < arr.length; i++) {
  if (i === 0) {
    arr.push('c');
  }
  console.log(`key: ${i}, value: ${arr[i]}`);
}

// 会有 3 行输出
```

<!-- links -->
[mdn btoa]: https://developer.mozilla.org/zh-CN/docs/Web/API/WindowBase64/btoa#Unicode_字符串
[escape]: http://www.w3school.com.cn/jsref/jsref_escape.asp
[code unit]: https://zh.wikipedia.org/wiki/%E5%AD%97%E7%AC%A6%E7%BC%96%E7%A0%81#.E5.AD.97.E7.AC.A6.E9.9B.86.E3.80.81.E4.BB.A3.E7.A0.81.E9.A1.B5.EF.BC.8C.E4.B8.8E.E5.AD.97.E7.AC.A6.E6.98.A0.E5.B0.84
[code point]: https://zh.wikipedia.org/wiki/%E7%A0%81%E4%BD%8D
[ecma-262 5.1 source text]: http://ecma-international.org/ecma-262/5.1/#sec-6
[baike unicode]: http://baike.baidu.com/link?url=x4NrU5EeRTT_QhQlBExvAGMsbUgDjcyqBvoo7Gvl-073Shjui9IvHyVU4pgF1wsEZOgAzEUXMr0PXANhsZ4AGa
[unicode & utf-8]: https://www.zhihu.com/question/23374078
[encodeURIComponent definition]: http://www.ecma-international.org/ecma-262/6.0/#sec-encodeuricomponent-uricomponent
[mdn charCodeAt]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/charCodeAt
[ecma-262 2015 charCodeAt]: http://www.ecma-international.org/ecma-262/6.0/#sec-string.prototype.charcodeat
[ecma-262 2015 codePointAt]: http://www.ecma-international.org/ecma-262/6.0/#sec-string.prototype.codepointat
[ecma-262 2015 fromCharCode]: http://www.ecma-international.org/ecma-262/6.0/#sec-string.fromcharcode
[wiki UTF-16]: https://zh.wikipedia.org/wiki/UTF-16
[html spec]: ./html_spec.md
[mdn Object.is]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/is
[ecma-262 2015 Object.is]: http://www.ecma-international.org/ecma-262/6.0/#sec-object.is