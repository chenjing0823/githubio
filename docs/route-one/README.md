# 笔记

> 一些知识点笔记

## for...in

数组拿index，对象拿key
``` js
const arr = [{a: 1}, 2, 'sad']
for (let i in arr) {
    console.log(i)
}
// 0
// 1
// 2
```
``` js
const obj = {a: 1, b: 's', c: [1,2,3]}
for (let i in obj) {
    console.log(i)
}
// a
// b
// c
```
## for...of

数组拿值，对象报错，map拿键值对
``` js
const arr = [{a: 1}, 2, 'sad']
for (let i of arr) {
    console.log(i)
}
// {a: 1}
// 2
// sad
```
``` js
let iterable = new Map([["a", 1], ["b", 2], ["c", 3]]);

for (let entry of iterable) {
  console.log(entry);
}
// ["a", 1]
// ["b", 2]
// ["c", 3]

for (let [key, value] of iterable) {
  console.log(value);
}
// 1
// 2
// 3

```

## Object 和 Map

对于多数Web开发任务来说，选择Object还是Map只是个人偏好问题，影响不大。

对于在乎内存和性能的开发者来说，对象和映射之间确实存在显著的差别。

+ 相同内存大小：Map大约可以比Object多存储50%的键/值对
+ 向Object和Map中插入新键/值对的消耗大致相当，不过插入Map在所有浏览器中一般会稍微快一点儿。
+ 如果代码涉及大量查找操作，那么某些情况下可能选择Object更好一些。
+ 如果代码涉及大量删除操作，那么毫无疑问应该选择Map

## weakMap 和 Map

使用没区别

[Object、map与weakmap三者在JavaScript中有什么不同](https://www.yisu.com/zixun/369156.html)

[Map与WeakMap的区别与使用](https://blog.csdn.net/qq_26834399/article/details/105071907)

[【ES6基础】Map与WeakMap](https://blog.csdn.net/Ed7zgeE9X/article/details/89879406)

## ES6 Map 和 Set

[ES6 Map 与 Set](https://www.runoob.com/w3cnote/es6-map-set.html)


## http缓存

[参考链接](https://segmentfault.com/a/1190000017962411)

### 强缓存：Cache-Control

**no-cache**：需要进行协商缓存，发送请求到服务器确认是否使用缓存。

**no-store**：禁止使用缓存，每一次都要重新请求数据。

**public**：可以被所有的用户缓存，包括终端用户和 CDN 等中间代理服务器。

**private**：只能被终端用户的浏览器缓存，不允许 CDN 等中继缓存服务器对其缓存。

**Cache-Control 与 Expires 可以在服务端配置同时启用，同时启用的时候 Cache-Control 优先级高。**

### 协商缓存 Last-Modify/If-Modify-Since

浏览器第一次请求一个资源的时候，服务器返回的 header 中会加上 Last-Modify，Last-modify 是一个时间标识该资源的最后修改时间。

当浏览器再次请求该资源时，request 的请求头中会包含 If-Modify-Since，该值为缓存之前返回的 Last-Modify。服务器收到 If-Modify-Since 后，根据资源的最后修改时间判断是否命中缓存。

如果命中缓存，则返回 304，并且不会返回资源内容，并且不会返回 Last-Modify。

缺点:

短时间内资源发生了改变，Last-Modified 并不会发生变化。

周期性变化。如果这个资源在一个周期内修改回原来的样子了，我们认为是可以使用缓存的，但是 Last-Modified 可不这样认为,因此便有了 ETag。

### 协商缓存 ETag/If-None-Match

与 Last-Modify/If-Modify-Since 不同的是，Etag/If-None-Match 返回的是一个校验码。ETag 可以保证每一个资源是唯一的，资源变化都会导致 ETag 变化。服务器根据浏览器上送的 If-None-Match 值来判断是否命中缓存。

与 Last-Modified 不一样的是，当服务器返回 304 Not Modified 的响应时，由于 ETag 重新生成过，response header 中还会把这个 ETag 返回，即使这个 ETag 跟之前的没有变化。

**Last-Modified 与 ETag 是可以一起使用的，服务器会优先验证 ETag，一致的情况下，才会继续比对 Last-Modified，最后才决定是否返回 304。**

### 缓存总结：

当浏览器再次访问一个已经访问过的资源时，它会这样做：

1.看看是否命中强缓存，如果命中，就直接使用缓存了。

2.如果没有命中强缓存，就发请求到服务器检查是否命中协商缓存。

3.如果命中协商缓存，服务器会返回 304 告诉浏览器使用本地缓存。

4.否则，返回最新的资源。

