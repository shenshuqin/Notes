**Cookie是什么：**

简单地说，cookie 就是浏览器储存在用户电脑上的一小段文本文件。cookie 是纯文本格式，不包含任何可执行的代码。一个 Web 页面或服务器告知浏览器按照一定规范来储存这些信息，并在随后的请求中将这些信息发送至服务器，Web 服务器就可以使用这些信息来识别不同的用户。大多数需要登录的网站在用户验证成功之后都会设置一个 cookie，只要这个 cookie 存在并可以，用户就可以自由浏览这个网站的任意页面。再次说明，cookie 只包含数据，就其本身而言并不有害。

#####  设置Cookie的失效时间：

如果Cookie没有设置expires属性，那么 cookie 的生命周期只是在当前的会话中，

关闭浏览器意味着这次会话的结束，此时 cookie 随之失效。

 1、当设置的失效时间大于等于1天时，我们可以在 expires 属性后面直接输入XX天数 

```js
Cookies.set('name', 'value', {
expires: 7,
});
 
// => 'value'
Cookies.get('name');
Cookies.remove('name');
```

2、当设置的失效时间少于一天时：我们需要在当前的时间上加上失效时间。

例如下面是设置cookie的失效时间为15分钟。

```js
var millisecond = new Date().getTime();
var expiresTime = new Date(millisecond + 60 * 1000 * 15);
 
Cookies.set('name', 'value', {
expires: expiresTime,
});
```

 PS：如果expires设置一个过去的时间点，那么这个cookie 会被立即删掉（失效）。 