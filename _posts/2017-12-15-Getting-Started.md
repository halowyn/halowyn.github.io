---
layout: post_layout
title: bootstrap制作官方网站和构建后台管理系统心得
time: On Friday, December 15, 2017
location: BeiJing
pulished: true
excerpt_separator: "```"
---

**1.关于col-sm-x和col-md-x栅格化布局的应用**

思考：好用的框架在场合适当的情况下才能充分发挥它的优势
背景：早前做his系统时也简单的应用了bootstrap的表格table模块，相当受益，对栅格化布局只是草草看了一眼，应用少没有过多了解。本次开发官方网站设计页面较为中规中矩，起初参考了官方文档的开发示例，简单的搭建了页面的header和footer，在做container部分时慢慢发现栅格化布局的曼妙所在，总结如下：

.col-xs- 超小屏幕 手机 (<768px)

.col-sm- 小屏幕 平板 (≥768px)

.col-md- 中等屏幕 桌面显示器 (≥992px)

.col-lg- 大屏幕 大桌面显示器 (≥1200px)
##### 代码
``` python
<div class="col-xs-12 col-sm-6 col-md-3 col-lg-3">
                        
</div>
```
屏幕尺寸

小于 768px 的时候，用 col-xs-12 类对应的样式；

在 768px 到 992px 之间的时候，用 col-sm-9 类对应的样式；

在 992px 到 1200px 之间的时候，用 col-md-6 类对应的样式；

大于 1200px 的时候，用 col-lg-3 类对应的样式；
当一个标签中同时包含这几个类名时，每个块所占的宽度将随着屏幕的大小自动适应，每个模块所占的宽度比例有col的number决定</div>

可以根据自己的需要在bootstrap.css里修改样式，需求里需要给每一列添加阴影，此时我们可以给列设置padding，列内元素单独设阴影。

**2.使用bootstrap的carousel制作首页banner的图片轮播效果**
思考：在学习和使用中尽量做到系统化，简化代码，模块化开发。
背景：做之前的项目一直使用的是swipper插件来做图片轮播，可以实现的效果较多，慢慢的公司的轮播模板就默认成一个固定的格式了，其实是那种比较常规的，本次做官网时，本着进一步熟悉bootstrap的初衷，深入的了解它的其他功能，在开发时尽量向bootstrap靠拢，于是就在文档中看到了这个轮播的api，现将其总结如下：

页面中首先写入代码：
##### 代码
``` python
<div id="carousel-example-generic" class="carousel slide my-slide" data-ride="carousel">
    <!-- Indicators -->
    <ol class="carousel-indicators"></ol>
    <!-- Wrapper for slides -->
    <div class="carousel-inner" role="listbox"></div>
</div>
```
根据标签释义，我们在inner里添加轮播的图片，在indicators里加入指向标。只是需要注意在添加时需要制定一个active的轮播。往往设置为第一个。通过js添加的方法如下：
``` python
$.each(data.data.banners,function(i,n){
   var img="'"+n.img+"'";
    if(i==0){
        str0+='<div class="item active">'
            +'<a class="item active" target="_blank" href='+n.url+'  style="background-image: url('+img+') "></a>'
            +'</div>';
        indicates+='<li data-target="#carousel-example-generic" data-slide-to='+i+' class="active"></li>'
    }else{
        str0+='<div class="item">'
            +'<a class="item" target="_blank" href='+n.url+'  style="background-image: url('+img+') "></a>'
            +'</div>'
        indicates+='<li data-target="#carousel-example-generic" data-slide-to='+i+'></li>'
    }

});
$('.carousel-inner').append(str0);
$('.carousel-indicators').append(indicates);
```
这样看起来就更加简单了，根本不需要去用其他的插件了，that‘s great！
**3.使用媒体查询来调整样式**
背景：这里用到媒体查询主要是为了做页面头部，因为头部的划分无法整除2，所以没办法使用栅格化布局，所以就用了百分比，但是在手机上展示时，字明显换行，有些logo也很大，影响页面美观，所以就在媒体查询中进行了调整：
``` 
@media screen and (max-width:767px){}
@media screen and (max-width:990px){}
``` 
**4.解决js的hover事件循环多次执行的问题**
使用stop(),每次执行动作的时候先将其他的动作停止掉。
使用方法如下：
``` 
 $('.intro').stop().hide(510);
 $('.intro').eq(index).stop().show(510);
```
**5.在angular中使用bootstrap模态框简便的设置弹窗提示并且传递数据**
背景：在制作后台管理系统时，需要进行增删改查，尤其是删除时，为了防止用户错删，保护数据，做弹窗提示是很有必要的。但是传统的浏览器自带的弹窗比较看上去不够美观，操作起来也不那么流畅，所以在引入bootstrap的情况下，我们值得做此尝试。

调用模态框的模板代码如下：
``` 
 <div class="modal fade" id="myModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel">
    <div class="modal-dialog" role="document">
        <div class="modal-content">
            <div class="modal-header">
                <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span aria-hidden="true">&times;</span></button>
                <h4 class="modal-title" id="myModalLabel">提示</h4>
            </div>
            <div class="modal-body">
                您确定要删除本条数据吗？
            </div>
            <div class="modal-footer">
                <button type="button" class="btn btn-default" data-dismiss="modal">关闭</button>
                <button type="button" class="btn btn-primary" id="delete" ng-click="deletedata()">删除</button>
            </div>
        </div>
    </div>
</div>

<li data-toggle="modal" data-target="#myModal"  ng-click="deliverdata(d)"><a href="#">删除</a></li>
```

在deliverdata方法中传递参数，然后在模态框中点击进行相应的修改，隐藏模态框。
``` 
$('#myModal').modal('hide')
```