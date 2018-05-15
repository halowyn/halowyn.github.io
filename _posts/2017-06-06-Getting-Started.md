---
layout: post_layout
title: canvas绘图使用总结
time: On Tuesday, June 6, 2017
location: BeiJing
pulished: true
excerpt_separator: "```"
---
<div style="word-spacing: 10px;font-weight: bold">在做后台管理系统时，有时候需要对详细信息生成一个预览图以供管理者查看，如果页面元素简单的话可以直接用canvas画布进行绘制，但是当页面元素较为复杂且超过一屏的时候则需要使用插件html2canvas.js进行绘制，在此对于绘制过程中的一些难点和细节总结如下：</div>

##### 一.直接通过canvas绘制

疑难点：
1.  在绘制图片时默认的不允许图片跨域
2.  在画布中绘制文字无法自动换行
3.  绘制图片，图片加载的img.src的位置放在img.onload上边提示画布被污染

对应的解决方案：
1. 当页面中存在若干张图片时，每次绘制之前new一个image，然后为它设置属性img.crossOrigin="Anonymous";注意区别大小写
2. 这里主要用到fillText和measureText属性：
 -  fillText（text,x,y）将一定的字符串绘制在以x,y的横坐标和纵坐标为起点的位置；
 - measureText(str).width/height,画布中的本属性用于测量字符串或者指定字符的款和高；
 - 具体实现：确定画布的宽度，左上的留白，内容区域的宽度，根据文字的长度进行循环，循环体内，每次遍历变量lineWidth+=ctx.measureText(str[i]).width; 当linewidth达到每行限制的宽度，就用str.substring进行字符串切割，然后用filltext绘制，当最后一行的宽度不足行宽时，就从上个截取后的字符串开始，到最后一个组成一组填充
3. 绘制图片时，图片加载的img.src的位置放在img.onload下边
4. 绘制的时候画布的高度无法自动适应，需要直接规定好画布的高度（如果内容大小可控的话还好，复杂的情况下不建议这种方法；另有高见请指点）
       
相关代码如下：
```
    <textarea name="" id="" cols="30" rows="10"></textarea>
    <div class="btn">画布生成图片</div>
    <script src="../js/jquery-3.0.0.min.js"></script>
    <script>  
    
        $('.btn').click(function(){
            var val=$('textarea').val();
            cvs = document.createElement("canvas");
            cvs.width=600;
            canvasTextAutoLine(val,cvs,10,30,18);
        })
        var imgs=['http://mpic.tiankong.com/a76/ed5/a76ed520544bbe949d90abb688482722/640.jpg','7.png','4.jpg'],data=['1','2'];
        function canvasTextAutoLine(str,canvas,initX,initY,lineHeight){
            var ctx = canvas.getContext("2d"); 
            ctx.fillStyle = '#333';
            ctx.strokeStyle = '#333'; //设置笔触的颜色
            ctx.font = "normal 12px 'microsoft yahei', 'Hiragino Sans GB', 'Tahoma'";//设置字体
            var lineWidth = 0;
            var canvasWidth = canvas.width-20; 
            canvas.height=500;
            var lastSubStrIndex= 0; 
            for(var i=0;i<str.length;i++){ 
                lineWidth+=ctx.measureText(str[i]).width; 
                if(lineWidth>canvasWidth-initX){//减去initX,防止边界出现的问题
                    ctx.fillText(str.substring(lastSubStrIndex,i),initX,initY);
                    initY+=lineHeight;
                    lineWidth=0;
                    lastSubStrIndex=i;
                } 
                if(i==str.length-1){
                    ctx.fillText(str.substring(lastSubStrIndex,i+1),initX,initY);
                }
            }
            initY+=18;
            $.each(imgs,function(i,n){
                var img=new Image();
                img.crossOrigin="Anonymous";
                img.onload=function(){
                    var ctx=canvas.getContext("2d");
                    heights=img.height*100/img.width;
                    ctx.drawImage(img,50,initY,100,heights);
                    if(i==imgs.length-1){
                        var images=new Image();
                        images.src=cvs.toDataURL("image/png",'1');
                        $('body').append(images);
                    }
                    initY+=heights+20;
                }
                img.src=n;
            })
        }
    </script>  
```
##### 二、通过html2canvas.js绘制
疑难点：
        1. html2canvas.js同样需要解决图片跨域的问题
        2. 修改了js之后按照文档上的调用方法仍然无法调用成功
        
对应的解决方案：
       1. 修改js中的代码，找到ImageContainer函数进行修改
       2. 调用时将useCORS设置为true，如果设置allowTaint时，只能为false
   
js中的代码修改如下：
```
function ImageContainer(src, cors) {
        this.src = src;
        this.image = new Image();
        var self = this;
        this.tainted = null;
        this.promise = new Promise(function(resolve, reject) {
            self.image.onload = resolve;
            self.image.onerror = reject;
            if (cors) {
                // self.image.crossOrigin = "anonymous";
                self.image.crossOrigin = "";
            }
            // self.image.src = src;
        self.image.src = src+"?"+new Date().getTime();
            if (self.image.complete === true) {
                resolve(self.image);
            }
        });
    } 
```  
调用代码如下：
```
$('.btn').click(function(){
    html2canvas($('#gen'), {
        allowTaint: false,
        useCORS:true,
        // taintTest: false,
        onrendered: function(canvas) {
            canvas.id = "mycanvas";
            //生成base64图片数据
            var dataUrl = canvas.toDataURL("png");
            $('body').append('<img src='+dataUrl+' alt="" />');
           
        }
    });
})
```
##### 三、通过html2canvas.js截取整个标签，不限于1屏
疑难点：
1.html2canvas.js同样需要获取到标签的宽和高
2.同样的调用方法无法成功

对应的解决方案：
1.修改js中的代码，找到ImageContainer函数进行修改
2.修改调用方式

js中的代码段修改如下：

```
function ImageContainer(src, cors) {
    this.src = src;
    this.image = new Image();
    var self = this;
    this.tainted = null;
    this.promise = new Promise(function(resolve, reject) {
        self.image.onload = resolve;
        self.image.onerror = reject;
        // if (cors) {
        //     self.image.crossOrigin = "anonymous";
        // }
        // self.image.src = src;
        if (cors) {
            // self.image.crossOrigin = "anonymous";
            self.image.crossOrigin = "";
        }
        // self.image.src = src;
    self.image.src = src+"?"+new Date().getTime();
        if (self.image.complete === true) {
            resolve(self.image);
        }
    });
}
```
调用如下：

```
function domShot(domId) {
// 0.5.0-beta4方法
    html2canvas(document.querySelector("#" + domId),{
        allowTaint: false,
        useCORS:true,
        height: $("#"+domId).outerHeight() + 20
    }).then(function(canvas) {
        mengb(canvas.toDataURL());
    });
}         
```



    

            
        
