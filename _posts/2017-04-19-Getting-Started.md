---
layout: post_layout
title: 项目总结二
time: On Wednesday, April 19, 2017
location: BeiJing
pulished: true
excerpt_separator: "```"
---

<div style="word-spacing: 10px;color: #337ab7;font-weight: bold">表单提交 px转rem pc端与移动端相互转换 获取图片验证码 下拉加载 页面乱码 邮箱正则表达式 index</div>
---

### 1. 使用表单提交数据，要注意格式,有时候同样的数据post表单与ajax请求的方式不一样，要注意action，enctype的参数：

```
<form action="/vn/user/goFile" method="post" enctype="multipart/form-data" id="form">
    <input type="file" name="file" id="file" multiple="multiple" onchange="previewImage(this)">
    <input type = "button" value="上传" id="input" />
</form>
```

### 2. px转rem插件使用与设置
  * 进入插件目录 sublime-->首选项-->浏览插件
  * 将插件的目录copy进入此目录，然后重启sublime
  * 进入sublime-->首选项-->插件设置-->cssrem-->settings-user
  * 设置转化的比例，保留的位数，和允许转化的文档类型。

    ```
            {
             "px_to_rem":40,
             "max_rem_fraction_length":3,
             "available_file_types":[".html",".css",".less",".sass"]
            }
    ```

### 3. 判断PC端与移动端进行适配页面的跳转，将m文件放在PC端的代码文件下边，节省一个域名，但是要注意跳转的方式。具体代码如下：
 pc转移动
  ```
    function browserRedirect() {
      var sUserAgent = navigator.userAgent.toLowerCase();
      var bIsIpad = sUserAgent.match(/ipad/i) == "ipad";
      var bIsIphoneOs = sUserAgent.match(/iphone os/i) == "iphone os";
      var bIsMidp = sUserAgent.match(/midp/i) == "midp";
      var bIsUc7 = sUserAgent.match(/rv:1.2.3.4/i) == "rv:1.2.3.4";
      var bIsUc = sUserAgent.match(/ucweb/i) == "ucweb";
      var bIsAndroid = sUserAgent.match(/android/i) == "android";
      var bIsCE = sUserAgent.match(/windows ce/i) == "windows ce";
      var bIsWM = sUserAgent.match(/windows mobile/i) == "windows mobile";
      if (bIsIpad || bIsIphoneOs || bIsMidp || bIsUc7 || bIsUc || bIsAndroid || bIsCE || bIsWM) {
        window.location.href="m/page/index.html";
      } else {}
    }
  ```
移动转pc时用了字符串拼接 :
```
      else {
          var url=window.location.href.split('m/')[0];
          window.location.href=url+'index.html';
      }
```
  
### 4. 获取图片验证码的方式
 ``` 
    $(mod).attr('src','*?param='+n+'&mobile='+tel+'&t='+Math.random()); 

 ```

### 5. 验证手机号码（/^1(3|4|5|7|8)\d{9}$/）

### 6. 通过a链接下载文件代码
```
  <a href='+n.url+' class="download r"  download="newfilename">
```
### 7.下拉加载数据，并提示加载是否完毕：首先要判断页面是否滑动到底部，如果滑动到底部，加载数据，且在列表下面追加正在加载，当页面加载成功时去掉正在加载提示，直到再次滑动到底部，如此反复；如果没有数据了就追加加载完毕。
判断是否滑动到底部的代码如下：
```
$(window).scroll(function(){
  var scrollPos = $(this).scrollTop();
  var dbHiht = $(document).height();
  if(dbHiht - scrollPos - $(this).height() < 1){
    setTimeout(function(){
      page++;
      if(page<=total){
        load();
      }
    },1000)
  }
})
```
### 8. 修改头像的方法,通过上传文件的方式，将reader读取出来的图片base64位编码传给后台，将图片的src修改为后台返回的图片网址；如下
```
$('#file').on('change', function(){
  $('.imageBox').show();
    var reader = new FileReader();
    reader.onload = function(e) {
        url=obj.url+'***';
        $.ajax({
              type: 'POST',
              url: url,
              data: { baseStr: e.target.result ,folder:"upload"},
              success: success,
              dataType: "json"
            });
        function success(data){
          // console.log(data);
          $('.port img').attr('src',data.data.url);
        }
    }
    reader.readAsDataURL(this.files[0]);
});
```
### 9. 添加关注微博按钮，查找uid：查看源代码，uid表示的是自己的id，oid表示的我所查看的别人的微博
### 10. 判断被选中的button按钮
```
     $.each($("input[name='answer']"),function(i,n){
        if(n.checked==true){
            console.log(i)
        }
    })
```
### 11. 作为公共页面的公共部分的头部尾部，要慎重写左右padding，否则即使body宽度设置了100%，也依然不能填充整个页面。
### 12. 限制input type="file"上传的文件类型，
比如，上传的是ppt格式，添加属性
```
<input type="file" accept=".ppt,.pptx">
```
上传的是图片类型，添加input属性
 ```
 <input type="file" accept="image/*">

 ```

### 13. 设置radio选中与不选中的
```
     $('.bg input[type="radio"]').on('click',function(){
        if($(this).attr('checked')){$(this).attr('checked',false);}
        else{$(this).attr('checked','checked');}
    })
```
### 14. 当alert出现中文乱码时，在script标签内加入属性charset="gb2312"
### 15. 匹配邮箱格式：/^[a-zA-Z0-9_-]+@[a-zA-Z0-9_-]+(\.[a-zA-Z0-9_-]+)+$/ 
### 16. index的用法

```
    var index=$('#regi1 input').index($(this));
```
