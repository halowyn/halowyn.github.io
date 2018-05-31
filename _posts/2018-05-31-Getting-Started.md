---
layout: post_layout
title: vue项目总结
time: On Tuesday, May 31, 2018
location: BeiJing
pulished: true
excerpt_separator: "```"
---


##### 当前正在做的项目选择使用vue框架来做，现将目前遇到的问题进行总结：
**vue-cli配置移动端自适配（引入flexible）**
1.安装lib-flexible
	```
	npm i lib-flexible --save
	```
2.在入口文件main.js里引用
```
import 'lib-flexible'
```
3.安装px2rem-loader，在实际的开发中，使用flexible插件时 会自动把px转换成rem单位。在vue-cli中安装过lib-flexible之后 ，将px转换成rem
```
npm install px2rem-loader
```
4.配置px2rem-loader，参照网络上的相关文档，在vue-cli生成的webpack 配置中，vue-loader 的options和其他样式文件loader 最终都是由build/untils.js里的一个方法生成的。因此在build/untils.js文件里进行修改:
 - 在cssLoader后面加上一个px2remLoader即可
 - px2rem-loader的remUnit 选项意思是1rem=多少像素，结合lib-flexible，我们将px2remLoader的option.remUnit 设置成设计稿宽度的1/10，remUnit设置为75 
 - 并将px2remLoader 放进函数generateLoaders的 loaders数组中
 - 代码如下
 
```
   const cssLoader = {
    loader: 'css-loader',
    options: {
      minimize: process.env.NODE_ENV === 'production',
      sourceMap: options.sourceMap,
      importLoaders:3
    }
  }
  const px2remLoader = {
    loader: 'px2rem-loader',
    options: {
      remUnit: 75
    }
  }
  const postcssLoader = {
    loader: 'postcss-loader',
    options: {
      sourceMap: options.sourceMap
    }
  }

  // generate loader string to be used with extract text plugin
  function generateLoaders (loader, loaderOptions) {
    const loaders = options.usePostCSS ? [cssLoader,px2remLoader, postcssLoader] : [cssLoader,px2remLoader]

    if (loader) {
      loaders.push({
        loader: loader + '-loader',
        options: Object.assign({}, loaderOptions, {
          sourceMap: options.sourceMap
        })
      })
    }

    // Extract CSS when that option is specified
    // (which is the case during production build)
    if (options.extract) {
      return ExtractTextPlugin.extract({
        use: loaders,
        fallback: 'vue-style-loader'
      })
    } else {
      return ['vue-style-loader'].concat(loaders)
    }
  }
  
```
- 重启之后，在组件中写单位直接写px  然后在浏览器中的单位会变成rem



**vue-cli里实现banner图的轮播**
######之前在做移动端项目时，是直接使用swipper插件，然后插入数据，进行swipper初始化，就可以了，但是在vue里需要稍作修改，我们引用的是vue-awesome-swiper
1.安装vue-awesome-swiper
```
npm install vue-awesome-swiper --save
```
2.在入口文件里引入
```
import  VueAwesomeSwiper from 'vue-awesome-swiper'
import  'swiper/dist/css/swiper.css'
Vue.use(VueAwesomeSwiper, /* { default global options } */)
```
3.在对应的组件里引入

```
 import 'swiper/dist/css/swiper.css'
 import { swiper, swiperSlide } from 'vue-awesome-swiper'
```
4.在export中设置
- autoplay:true,写成具体数值的话无效

```

	export default {
	    name: 'Home',
	    data () {
	      return {
	        bannerList: "",
	        tillerList: "",
	        camelliaList: "",
	        swiperOption: {
	          initialSlide: 0,
	           pagination: {
	            el: '.swiper-pagination'
	           },
	          autoplay: true,
	          loop: true,
	          speed: 510,
	          direction: 'horizontal'
	          mousewheelControl: true,
	          spaceBetween: 30,
	          autoplayDisableOnInteraction: false,
	          observer: true,
	          observeParents: true
	        }
	      }
	    },
	    components: {
	      swiper,
	      swiperSlide
	    },
	    created:function(){
	    var that = this;
	    ******//http请求数据
	    }).then(function(res){
	      that.bannerList=res.data.data.bannerList
	      that.camelliaList=res.data.data.camelliaList
	    },function(){
	    });
	    },
	    computed: {
	      swiper() {
	          return this.$refs.mySwiper.swiper;
	      }
	    },
	    mounted () {
	      //这边就可以使用swiper这个对象去使用swiper官网中的那些方法
	      this.swiper.slideTo(0, 1000, false);
	    }
	  }
```
5.在template里写入(ps:v-if不写的话首次循环从第一个开始，往后每次都是从第二个开始)
```
<swiper v-if="bannerList" :options="swiperOption"  ref="mySwiper" >
      <!-- 这部分放你要渲染的那些内容 -->
      <swiper-slide v-for="(item) in bannerList" :key="item.id">
        <img :src="item.img" class="img" alt="">
      </swiper-slide>
      <!-- 这是轮播的小圆点 -->
      <div class="swiper-pagination swiper-pagination-clickable swiper-pagination-bullets" slot="pagination">
        <!--<span v-for="item in bannerList" :key="item.id" class="swiper-pagination-bullet"></span>-->
      </div>
    </swiper>
```

6.样式(ps:很重要)
```
.swiper-container{
    width: 750px;
    height: 330px;
    overflow: hidden;
  }
  .swiper-wrapper{
    height: 330px;
  }
  .swiper-container img{
    width: 100%;
    height:100%;
  }
  .swiper-slide{
    float: left
  }
```