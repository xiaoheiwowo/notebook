# js

## for

```js
for (var i = 0; i < 10; i++){}
// ES6
for (let i in books){} // i是索引
for (let b of books){} // b是对象
```

```js
// var let 
{
    let a = "a";
    var b = "b";
}
console.log(a)
console.log(b)
// 闭包解决作用域问题，for 和 if 都没有块级作用域，let 有块级作用域
var btns = document.getElementsByTagName("button");
for (var i = 0; i < btns.length; i++) {
    (function(i){
        btns[i].addEventListener("click", function() {
            console.log(i)
        })
    })(i)
}

// const 
const a = "str"
const b = {
    b: "str1"
    a,
    run(){},
}

```

