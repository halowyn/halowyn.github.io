---
layout: post_layout
title: 抽奖项目总结
time: On Wednesday, May 3, 2017
location: BeiJing
pulished: true
excerpt_separator: "```"
---
<div style="word-spacing: 10px;color: #337ab7;font-weight: bold">控制radio和checkbox是否选中 转盘抽奖</div>

### 单选和多选
* 系统默认的radio和checkbox小，可以自行设置大小，但是要给radio设置border-radius：100%；
* 在题目中不同的地方可以用不同的class作区分
* 遍历一个对象里面有没有与数组中的相同的值

```python
  $.each($('.exam'),function(i,n){
      if(arr.indexOf($(n).attr('id'))!=-1){
        $(n).addClass('b');
      }else{
        $(n).addClass('c');
      }
    })
```
* 单选按钮name相同的话没有多选的可能性，但是复选框，

 1.将复选框放在一个div里面，使得整个div都有点击的效果的话，使用click事件，但是
 2.每个题目有规定的选择个数，如果选够了个数的话，要将其他的选项禁止掉，
 3.问题在于，这么禁止了之后，复选框就无法选择了。
 
 解决办法：复选框无法选择是因为他的触发跟标签的触发条件不一致，所以先获取一下点击事件的e.target.value,判断是否为on，然后将on和undefined区别解决就可以了；代码如下：

```python
if(e.target.value=="on"){
   $(this).find('input:checkbox').eq(0).prop("checked");
}else{
   if($(this).find('input').eq(0)[0].checked==true){
    $(this).find('input').eq(0)[0].checked=false;
   }else if($(this).find('input').eq(0)[0].checked==false){
    $(this).find('input').eq(0)[0].checked=true;
    if($(this).find('input').eq(0)[0].disabled=="false"){
      $(this).find('input').eq(0)[0].checked="true";
    }
   }
}

//设置其他复选框为只读的方式：
if(counts==chooseonly){
    $.each($(this).closest('.exam').find('input[type=checkbox]'),function(i,n){
        if(!$(n)[0].checked){
            $(n).attr("disabled", "disabled");
      }
    })
}

```

## 转盘抽奖
* 使用jqueryrotate.js,但是要对中奖区域的旋转角度进行判断，根据后台返回的中奖与否，决定旋转重点。
