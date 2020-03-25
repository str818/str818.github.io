---
layout: article
title: RESTful API 最佳实践
tags: Web
 
lang: zh-Hans
key: RestFul_API
pageview: true
toc: true
show_subscribe: false
---

## 一、什么是 RESTful?

REST，**Resource Representational State Transfe** 的缩写，翻译过来就是 **“资源”在网络传输中以某种“表现形式”进行“状态迁移”**。如果一个架构符合 REST 原则，就称它为 RESTful 架构。

- **资源（Resource）**: 可以把真实的对象数据称为资源。一个资源既可以是一个集合，也可以是单个个体。比如我们的班级 classs 是代表一个集合形式的资源，而特定的 class 代表单个个体资源。每一种资源都有特定的 URI（统一资源定位符）与之对应，如果我们需要获取这个资源，访问这个 URI 就可以了，比如获取特定的班级：`/class/12`。另外，资源也可以包含子资源，比如 `/classs/classId/teachers`：列出某个指定班级的所有老师的信息。

- **表现形式（Representational）**: 「资源」是一种信息实体，它可以有多种外在表现形式。我们把"资源"具体呈现出来的形式比如 json、xml、image、txt 等等叫做它的「表现层/表现形式」。

- **状态转移（State Transfer）**: REST 中的状态转移更多地描述的服务器端资源的状态，比如通过增删改查（通过 HTTP 动词实现）引起资源状态的改变。ps:互联网通信协议 HTTP 协议，是一个无状态协议，所有的资源状态都保存在服务器端。

综上，RESTful 架构可以理解为：

1. 一个 URI 代表一种资源；
2. 客户端和服务器之间，传递这种资源的某种表现形式比如 json、xml、image、txt 等等；
3. 客户端通过特定的 HTTP 动词，对服务器端资源进行操作，实现"表现层状态转化"。

## 二、API 设计规范

### 1. 动词

RESTful 的核心思想就是，客户端发出的数据操作指令都是「动词 + 宾语」的结构。比如, `GET /articles` 这个命令，`GET` 是动词，`/articles` 是宾语。

动词通常就是五种 HTTP 方法，对应 CRUD 操作。

- GET：读取（Read）
- POST：新建（Create）
- PUT：更新（Update），整体更新
- PATCH：更新（Update），部分更新
- DELETE：删除（Delete）

### 2. 宾语

宾语就是 API 的 URL，是 HTTP 动词作用的对象。它应该是名词，不能是动词，同时名词也应该使用复数。比如，`/articles` 这个 URL 就是正确的，而下面的 URL 不是名词，所以都是错误的。

如果 API 调用不涉及资源（如计算、翻译等操作）的话，可以用动词。比如 `GET /calculate?param1=1`。

理清资源的层次结构，比如业务针对的范围是学校，那么学校会是一级资源:`/schools`，老师: `/schools/teachers`，学生: `/schools/students` 就是二级资源。

```
GET    /classs：列出所有班级
POST   /classs：新建一个班级
GET    /classs/classId：获取某个指定班级的信息
PUT    /classs/classId：更新某个指定班级的信息（一般倾向整体更新）
PATCH  /classs/classId：更新某个指定班级的信息（一般倾向部分更新）
DELETE /classs/classId：删除某个班级
GET    /classs/classId/teachers：列出某个指定班级的所有老师的信息
GET    /classs/classId/students：列出某个指定班级的所有学生的信息
DELETE classs/classId/teachers/ID：删除某个指定班级下的指定的老师的信息
```

常见的情况是，资源需要多级分类，因此很容易写出多级的 URL，例如上面的 `DELETE classs/classId/teachers/ID`，这种 URL 不利于扩展，语义也不明确，更好的做法是，除了第一级，其他几杯都用查询字符串表示，例如 `DELETE classs/classId?teachers=ID`。


## 三、状态码

客户端的每一次请求，服务器都必须给出回应。回应包括 HTTP 状态码和数据两部分。HTTP 状态码就是一个三位数，分成五个类别。

- 1xx：相关信息（API 不需要）
- 2xx：操作成功
- 3xx：重定向
- 4xx：客户端错误
- 5xx：服务器错误

### 1. 2xx

200 状态码表示操作成功，但是不同的方法可以返回更精确的状态码。

```
GET: 200 OK (操作成功)
POST: 201 Created （生成新资源）
PUT: 200 OK
PATCH: 200 OK
DELETE: 204 No Content（资源不存在）
```

202 状态吗表示服务器已经收到请求，但还未进行处理，会在未来再处理，通常用于异步操作。

### 2. 3xx

API 用不到 301 状态码（永久重定向）和 302 状态码（暂时重定向)，因为它们可以由应用级别返回，浏览器会直接跳转，API 级别可以不考虑这两种情况。

### 3. 4xx

4xx 状态码表示客户端错误，主要有下面几种。

- 400 Bad Request：服务器不理解客户端的请求，未做任何处理。
- 401 Unauthorized：用户未提供身份验证凭据，或者没有通过身份验证。
- 403 Forbidden：用户通过了身份验证，但是不具有访问资源所需的权限。
- 404 Not Found：所请求的资源不存在，或不可用。
- 405 Method Not Allowed：用户已经通过身份验证，但是所用的 HTTP 方法不在他的权限之内。

### 4. 5xx

5xx 状态码表示服务端错误。一般来说，API 不会向用户透露服务器的详细信息，所以只要两个状态码就够了。

- 500 Internal Server Error：客户端请求有效，服务器处理时发生了意外。
- 503 Service Unavailable：服务器无法处理请求，一般用于网站维护状态。