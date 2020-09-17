---
title: swiper 使用体验
date: 2020-09-16 21:20:11
tags: js
categories: 前端
---


### 首先是实例化swiper  
这里有一个注意点，就是**实例化的时机**  

如果你的swiper内容是写死的，可以在*componentDidMount*中实例化，但是如果你的内容是通过接口异步请求过来的，就必须在*componentDidUpdate*里实例化，因为如果在 *componentDidMount* 中实例化，异步请求过来的内容可能还没有挂载完就实例化就会出现问题
```
componentDidUpdate(){
      this.initialBannerSwiper()
}
```
这里还用到了一个知识点，就是**单例模式**，因为*componentDidMount*会多次执行，所以使用单例模式避免生成多个实例


```
initialBannerSwiper =()=>{
        if(this.bannerSwiper){
            return this.bannerSwiper  //单例模式
        }
        this.bannerSwiper = new Swiper('#swiper-banner', {

            slidesPerView: 1, //设置slider容器能够同时显示的slides数量(carousel模式) 默认值为1。

            observer: true,  //当改变swiper的样式（例如隐藏/显示）或者修改swiper的子元素时，自动初始化swiper。

            observeParents: true, //将observe应用于Swiper的父元素。当Swiper的父元素变化时，例如window.resize，Swiper更新。

            shortSwipes: false,  // 这个属性后面会说

            slideToClickedSlide: false, //设置为true则点击slide会过渡到这个slide。

            loop: true,  //开启循环

            autoplay: true, //设置为true启动自动切换，并使用默认的切换设置。

            pagination: {
                clickable: true, //此参数设置为true时，点击分页器的指示点分页器会控制Swiper切换。
                el: '#swiper-pagination-banner',
            }

        })
        this.hoverStop()
}


```  

**************  

###鼠标划入停止轮播

swiper居然没有这个api来控制，好吧 只能自己来用js来控制了  

#####*bannerSwiper.$el*
swiper的container的[Dom7/jQuery对象](https://www.swiper.com.cn/usage/dom7/)
>Swiper4自带有DOM7库，因此无需另外加载Jquery等库即可对Dom7对象使用以下常用的DOM操作

this.bannerSwiper.$el[0]就可以拿到 实例的container的dom节点
```
hoverStop = ()=>{
        let that = this.bannerSwiper.$el[0] // 实例的container的dom节点

        that.addEventListener("mouseenter", () => {
            this.bannerSwiper.autoplay.stop()
            that.addEventListener("mousemove", () => {
            })
        })
        that.addEventListener("mouseleave",()=>{
            this.bannerSwiper.autoplay.start()
        })
}
```
######你可能还会遇到一个小问题
这也是我遇到的，那就是鼠标划到轮播图的时候点击轮播图 会偶尔出现切换的情况，swiper有一个属性可以控制 *shortSwipes*  ,官方描述是这样的
>默认允许短切换。设置为false时，只能长切换，进行快速且短距离的滑动无法触发切换。

为什么会出现这种情况呢，因为swiper是专注于移动端的，划上swiper点击的时候可能鼠标方向偏了一点，swiper会以为你是手机上的滑动，设置为false时，短距离的滑动就不会发生切换了，下面是官方的说法


> ###Swiper常用于移动端网站的内容触摸滑动
>Swiper是纯javascript打造的滑动特效插件，面向手机、平板电脑等移动终端。
>Swiper能实现触屏焦点图、触屏Tab切换、触屏多图切换等常用效果。
>Swiper开源、免费、稳定、使用简单、功能强大，是架构移动终端网站的重要选择！
###最后
记得在组件卸载的时候解绑事件和销毁swiper实例哦
```
componentWillUnmount(){
        if(this.bannerSwiper){
            this.bannerSwiper.detachEvents()
            this.bannerSwiper.destroy()
        }
}
```  



  

swiper真的强大，了解更多请参考 [swiper 文档](https://www.swiper.com.cn/api/index.html)  

#####如果对您有帮助记得给个 *喜欢* 哦
