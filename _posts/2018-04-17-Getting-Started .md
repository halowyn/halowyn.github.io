---
layout: post_layout
title: 小程序 答题王
time: On Tuesday, April, 2018
location: BeiJing
pulished: true
excerpt_separator: "```"
---

<h3>1.WAService.js:3 thirdScriptError  
this.setData is not a function;at pages/index/index onLoad function;报错的解决办法</h3>
思考： 在wx.getStorage里直接赋值this.setData报此错误，在函数里打印this，指向该构造函数本身，说明此时的指向错误，因此在使用this.setData时，要考虑此时的this指向。
<div style="color: red">
    解决办法：在该函数外层将this赋值给that，之后再赋值。var that=this常用
</div>

<h3>获取用户权限</h3>
思考：当小程序里需要用到用户信息时，需要先获取用户权限，在用户首次登录该小程序时，提醒用户进行授权。
解决办法：
<h6>1.在app.js里onlaunch函数里调用getUser函数</h6>
``` python
 getuser() {
    var obj = this;
    wx.getUserInfo({
      success: res => {
        // 可以将 res 发送给后台解码出 unionId
        this.globalData.userInfo = res.userInfo
        if (this.userInfoReadyCallback) {
          this.userInfoReadyCallback(res)
        }
      }, fail() {
        //用户拒绝则不然操作
        wx.openSetting({
          success: (res) => {
            obj.getuser();
          }
        });
      }
    })
  },
```
<h6>2.在index.js里定义hasUserInfo: false,
    canIUse: wx.canIUse('button.open-type.getUserInfo'),然后在onload函数里写入如下代码</h6>
``` python
    if (app.globalData.userInfo) {
      this.setData({
        userInfo: app.globalData.userInfo,
        hasUserInfo: true
      })
      console.log(this.data.userInfo)
    } else if (this.data.canIUse){
      // 由于 getUserInfo 是网络请求，可能会在 Page.onLoad 之后才返回
      // 所以此处加入 callback 以防止这种情况
      app.userInfoReadyCallback = res => {
        this.setData({
          userInfo: res.userInfo,
          hasUserInfo: true
        })
        console.log(this.data.userInfo)
      }
    } else {
      // 在没有 open-type=getUserInfo 版本的兼容处理
      wx.getUserInfo({
        success: res => {
          this.setData({
            userInfo: res.userInfo,
            hasUserInfo: true
          })
          console.log(this.data.userInfo)
        }
      })
    }
```