---
layout: post_layout
title: Node.js HTTP开发遇到的若干问题
time: On Monday, April, 2018
location: BeiJing
pulished: true
excerpt_separator: "```"
---

<h3>1. Error: getaddrinfo ENOTFOUND http://xx.dd.com</h3>
<div style="color: red">
    解决方法：在node.js中使用http或者https发送请求的时候host不要带http://或者https://,直接写xx.yy.com即可。
</div>

```
