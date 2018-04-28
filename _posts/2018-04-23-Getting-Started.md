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

<h3>2. Error: cannot post / 报错</h3>
<div style="color: red">
    解决方法：由于项目工期太短，以$http方式的post请求没找到合适的方法，情急之下就换成了ajax的post方法，可以了
</div>

<h3>3.connect ECONNREFUSED 127.0.0.1:3000解决方案</h3>
<div style="color: red">
    解决方法：尝试了网上的说法npm config set proxy null，无效，后来在配置文件里修改 "port": process.env.PORT || 3000
</div>

<h3>4.WebStorm警告unresolved function or method()</h3>
<div>解决办法：1.在WebStorm 2016.x-2017.x中：确保启用了Node.js Core库Settings (Preferences) | Languages & Frameworks | Node.js and NPM</div>

<h3>5.AngularJs中select绑定ng-model数字类型绑定问题：

使用ng-model绑定select的时候默认是string字符串，如果指定的number值会绑定失败</h3>


  <div ng-app='module' ng-controller="myCtrl">  
        请选择性别：  
        <select name="sex" ng-model='sex' >   
            <option value="">请选择</option>  
            <option value="1">男</option>  
            <option value="2">女</option>  
        </select>  
        <button type="" ng-click="alter();">修改</button>  
    </form> 
``` python 
    <script>  
    (function() {  
        'use strict';  
       var app= angular.module('module', [  
        ]);  
       app.controller('myCtrl',function($scope){  
           $scope.sex="1"; //特别说明，此处指定的为字符串，如果是number类型则绑定不成功  
          console.info($scope);  
          $scope.alter=function(){  
              $scope.sex="2";  
               console.info($scope);  
          }  
       });  
    })();  
    </script>
```
指定数值类型，绑定失败
<div style="color: red">解决办法：如果需要指定number类型的值，对于select的绑定使用ng-options方式
 请选择性别：  
<select name="sex" ng-model='sex'  ng-options='x.id as x.name for x  in [{id:1,name:"男"},{id:2,name:"女"}]'>   
<option value="">请选择</option>  
</select>  
如果不一定非得number类型，在修改$scope的时候指定string类型就行了。
</div>


