---
title: 基于vue+axios解决跨域的问题
date: 2018-12-18 09:00:15
tags:
---

本文是通过vue-cli的proxyTable可以解决跨域，还有通过ngnix的方法，本文暂不提供了。

**解决思路**：先使用axios实现一个简单的网络请求，再设置跨域会显得比较简单。

首先找到config/index.js,找到proxyTable参数，设置成：  

```
'/api': {
        target: 'http://192.168.1.100/xxx',//api共同的部分
        pathRewrite: {
          '^/api': ''
        },
      }
```
**解析一下**：'/iclient': {}, 表示是'/iclient'开头的才用代理.  用''^/iclient'':'', 把'/iclient'去掉

第二步，在axios访问的时候，路径前缀一定要加上api，如下：  

```
export function login(username, password) {
  return request({
    url: '/api/test',
    method: 'post',
    data: {
      username,
      password
    }
  })
}
```
注意，在浏览器中的实际路径就是代理的路径，而不是真实的。例如：我这个例子的访问路径是**http://localhost:9528/api/test。**
（如果你也是基于vue-admin-template来做的，注意那个返回的数据格式那个code必须是int型的20000喔，不然会报错的）

哈哈哈 好像真的有点简单，我还整了一天半这个东西，不过做出来的时候还是很开心的~
