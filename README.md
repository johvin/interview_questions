# Interview Questions

根据 [whatwg html 规范][whatwg html]、[w3c html5 规范][w3c html5] 和 [ECMAScript 2015 规范][ecma-262 2015 spec] 及其他前端知识总结的一些技术问题，可以用作面试题考察面试者。来源包括个人思考以及网络上的一些面试题。欢迎大家提 issue 或者 pr 贡献。

## 入口

- [HTML 面试题](./html_spec.md)
- [Javascript 面试题](./ecma_spec.md)
- [Http 面试题](./http.md)

## 进度

### [whatwg html 规范][whatwg html] 相关

1. 8 Web application APIs
    + 8.2 The WindowOrWorkerGlobalScope mixin
    + 8.3 Base64 utility methods
    + 8.5 Timers



### [w3c html5 规范][w3c html5] 相关

1. 8 The HTML syntax
    + [8.2.6][w3c 8.2.6] The end (部分)
    + [4.11.1][w3c 4.11.1] The script element
        * [4.11.1.15][w3c 4.11.1.15] prepare a script step-15


### [ECMAScript 2015 规范][ecma-262 2015 spec] 相关

1. 7 Abstract Operations
    + 7.1 Type Conversion
    + 7.2 Testing and Comparison Operations
        * [7.2.9][ecma-262 7.2.9] SameValue(x, y)
        * 7.2.10 SameValueZero(x, y)
        * 7.2.13 Strict Equality Comparison
1. 10 ECMAScript Language: Source Code
    + [10.1][ecma-262 2015 10.1] & [ES5.1 6][ecma-262 5.1 6] Source Text
        * 10.1.1 Static Semantics: UTF16Encoding ( cp )
        * 10.1.2 Static Semantics: UTF16Decode( lead, trail )
1. 18 The Global Object
    + 18.2 Function Properties of the Global Object
        * 18.2.6 URI Handling Functions
            - 18.2.6.1 URI Syntax and Semantics
                + [18.2.6.1.1][ecma-262 2015 18.2.6.1.1] Runtime Semantics: Encode ( string, unescapedSet )
            - [18.2.6.5][ecma-262 2015 18.2.6.5] encodeURIComponent (uriComponent)
1. 19 Fundamental Objects
    + 19.1 Object Objects
        * [19.1.2][ecma-262 2015 19.1.2] Properties of the Object Constructor
            - 19.1.2.10 Object.is ( value1, value2 )
1. 21 Text Processing
    + 21.1 String Objects
        * 21.1.2 Properties of the String Constructor
            - [21.1.2.1][ecma-262 2015 21.1.2.1] String.fromCharCode ( ...codeUnits )
            - 21.1.2.2 String.fromCodePoint ( ...codePoints )
        * 21.1.3 Properties of the String Prototype Object
            - 21.1.3.1 String.prototype.charAt ( pos )
            - [21.1.3.2][ecma-262 2015 21.1.3.2] String.prototype.charCodeAt ( pos )
            - [21.1.3.3][ecma-262 2015 21.1.3.3] String.prototype.codePointAt ( pos )



[whatwg html]: https://html.spec.whatwg.org/multipage/
[w3c html5]: https://www.w3.org/TR/html5
[ecma-262 2015 spec]: http://www.ecma-international.org/ecma-262/6.0/
[w3c 8.2.6]: https://www.w3.org/TR/html5/syntax.html#the-end
[w3c 4.11.1]: https://www.w3.org/TR/html5/scripting-1.html#the-script-element
[w3c 4.11.1.15]: https://www.w3.org/TR/html5/scripting-1.html#script-processing-defer
[ecma-262 2015 10.1]: http://www.ecma-international.org/ecma-262/6.0/#sec-source-text
[ecma-262 5.1 6]: http://ecma-international.org/ecma-262/5.1/#sec-6
[ecma-262 7.2.9]: http://www.ecma-international.org/ecma-262/6.0/#sec-samevalue
[ecma-262 2015 18.2.6.5]: http://www.ecma-international.org/ecma-262/6.0/#sec-encodeuricomponent-uricomponent
[ecma-262 2015 18.2.6.1.1]: http://www.ecma-international.org/ecma-262/6.0/#sec-encode
[ecma-262 2015 19.1.2]: http://www.ecma-international.org/ecma-262/6.0/#sec-properties-of-the-object-constructor
[ecma-262 2015 21.1.2.1]: http://www.ecma-international.org/ecma-262/6.0/#sec-string.fromcharcode
[ecma-262 2015 21.1.3.2]: http://www.ecma-international.org/ecma-262/6.0/#sec-string.prototype.charcodeat
[ecma-262 2015 21.1.3.3]: http://www.ecma-international.org/ecma-262/6.0/#sec-string.prototype.codepointat