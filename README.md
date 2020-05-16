# 第一章节 介绍微信小程序的生命周期，什么是生命周期呢？

> 通俗的讲，生命周期就是指一个对象的生老病死。
>  从软件的角度来看，生命周期指程序从创建、到开始、暂停、唤起、停止、卸载的过程。
    
下面从一下三个方面介绍微信小程序的生命周期：

- 应用生命周期
- 页面生命周期
- 应用与页面生命周期

## 应用生命周期

![enter description here][1]

1. 用户首次打开小程序，触发 onLaunch（全局只触发一次）。
* 小程序初始化完成后，触发onShow方法，监听小程序显示。
* 小程序从前台进入后台，触发 onHide方法。
* 小程序从后台进入前台显示，触发 onShow方法。
* 小程序后台运行一定时间，或系统资源占用过高，会被销毁。

前台、后台定义： 当用户点击左上角关闭，或者按了设备 Home 键离开微信，小程序并没有直接销毁，而是进入了后台；当再次进入微信或再次打开小程序，又会从后台进入前台。

在整理本文资料的时候，有点不解，为什么不把小程序监听『销毁』方法开放给开发者，我猜测是因为IOS系统限制『按下Home键时，app从活动状态转入后台，会被挂起』；微信也不例外，只要运行一段时间或把微信客户端进程杀掉，就无法通知小程序应用被销毁。

》》》页面生命周期

![enter description here][2]

1. 小程序注册完成后，加载页面，触发onLoad方法。
* 页面载入后触发onShow方法，显示页面。
* 首次显示页面，会触发onReady方法，渲染页面元素和样式，一个页面只会调用一次。
* 当小程序后台运行或跳转到其他页面时，触发onHide方法。
* 当小程序有后台进入到前台运行或重新进入页面时，触发onShow方法。
* 当使用重定向方法wx.redirectTo(OBJECT)或关闭当前页返回上一页wx.navigateBack()，触发onUnload

》》》应用生命周期影响页面生命周期

![enter description here][3]

1. 小程序初始化完成后，页面首次加载触发onLoad，只会触发一次。 
* 当小程序进入到后台，先执行页面onHide方法再执行应用onHide方法。
* 当小程序从后台进入到前台，先执行应用onShow方法再执行页面onShow方法。

下图是小程序从注册到页面加入，前后台切换流程。
![enter description here][4]

  [1]: ./assets/images/1应用生命周期.png "1应用生命周期.png"
  [2]: ./assets/images/2页面生命周期.png "2页面生命周期.png"
  [3]: ./assets/images/3应用与页面生命周期.png "3应用与页面生命周期.png"
  [4]: ./assets/images/小程序生命周期.gif "小程序生命周期.gif"


## 第二章 微信小程序之事件绑定

### 》》》什么是事件
- 事件是视图层到逻辑层的通讯方式。
- 事件可以将用户的行为反馈到逻辑层进行处理。
- 事件可以绑定在组件上，当达到触发事件，就会执行逻辑层中对应的事件处理函数。
- 事件对象可以携带额外信息，如id, dataset, touches。

### 》》》事件分类
- touchstart    手指触摸
- touchmove     手指触摸后移动
- touchcancel   手指触摸动作被打断，如弹窗和来电提醒
- touchend      手指触摸动作结束
- tap           手指触摸后离开
- longtap       手指触摸后后，超过350ms离开

### 》》》事件绑定

事件绑定的写法同组件的属性，以 key、value 的形式。

- key 以bind或catch开头，然后跟上事件的类型，如bindtap, catchtouchstart
- value 是一个字符串，需要在对应的 Page 中定义同名的函数。不然当触发事件的时候会报错。
bind事件绑定不会阻止冒泡事件向上冒泡，catch事件绑定可以阻止冒泡事件向上冒泡。

上面简单介绍了小程序事件基础，是时候彰显"事件"的威力：
- 单击(tap)
- 双击(dbtap)
- 长按(longtap)
- 滑动
- 多点触控

#### 1.单击
单击事件由touchstart、touchend组成,touchend后触发tap事件。

![](./assets/images/click.gif)

```html
<view>
  <button type="primary" bindtouchstart="mytouchstart" bindtouchend="mytouchend" bindtap="mytap">点我吧</button>
</view>
```

```javascript
mytouchstart: function(e){
    console.log(e.timeStamp + '- touch start')
},
mytouchend: function(e){
    console.log(e.timeStamp + '- touch end')
},
mytap: function(e){
    console.log(e.timeStamp + '- tap')
}
```

#### 2.双击
双击事件由两个单击事件组成，两次间隔时间小于300ms认为是双击；微信官方文档没有双击事件，需要开发者自己定义处理。

![](./assets/images/dbclick.gif)

```html
<view>
  <button type="primary" bindtap="mytap">点我吧</button>
</view>
```

```javascript
Page({
  data: {
    //上一次触摸距离页面打开时间毫秒数，默认为为0
    lastTapDiffTime: 0
  },
  //触摸事件，判断单击还是双击
  mytap: function(e){
    //触摸时间距离页面打开时间毫秒数
    var curTime = e.timeStamp;
    //上一次触摸距离页面打开时间毫秒数
    var lastTime = this.data.lastTapDiffTime;
    if(lastTime > 0){
      //如果两次单击间隔小于300毫秒，认为是双击
      if(curTime - lastTime < 300){
        console.log(e.timeStamp + '- db tap')
      }else{
        console.log(e.timeStamp + '- tap')
      }
    }else{
      console.log(e.timeStamp + '- first tap')
    }
    //将本次点击触摸时间设置为上一次触摸时间
    this.setData({lastTapDiffTime: curTime});
  }
})
```

#### 3.长按
长按事件手指触摸后，超过350ms再离开。

![](./assets/images/longtap.gif)

```html
<view>
  <button type="primary" bindtouchstart="mytouchstart" bindlongtap="mylongtap" 
    bindtouchend="mytouchend" bindtap="mytap">点我吧</button>
</view>
```

```javascript
mytouchstart: function(e){
    console.log(e.timeStamp + '- touch start')
},
//长按事件
mylongtap: function(e){
    console.log(e.timeStamp + '- long tap')
  },
mytouchend: function(e){
    console.log(e.timeStamp + '- touch end')
},
mytap: function(e){
    console.log(e.timeStamp + '- tap')
}
```

单击、双击、长按属于点触事件，会触发touchstart、touchend、tap事件，touchcancel事件只能在真机模拟，不多说了。
<table>
    <tr>
        <th>事件</th>
        <th>触发顺序</th>
    </tr>
    <tr>
      <td>单击</td>  
      <td>touchstart → touchend → tap</td>
    </tr>
    <tr>
      <td>双击</td>  
      <td>touchstart → touchend → tap → touchstart → touchend → tap</td>
    </tr>
    <tr>
      <td>长按</td>  
      <td>touchstart → longtap → touchend → tap</td>
    </tr>
</table>

#### 4.滑动

手指触摸屏幕并移动，为了简化起见，下面以水平滑动和垂直滑动为例。
滑动事件由touchstart、touchmove、touchend组成。

![](./assets/images/shoushi.gif)

坐标图：
![](./assets/images/coor.png)

1. 以屏幕左上角为原点建立直角坐标系。第四象限为手机屏幕，Y轴越往下坐标值越大（注意跟数学象限的区别）。
2. 假设A点为touchstart事件触摸点，坐标为A(ax,ay)，然后手指向上滑动到点B(bx,by)，就满足条件by < ay;
3. 同理，向右滑动到C(cx,cy),满足cx > ax；向下滑动到D(dx,dy),满足dy > ay；向左移动到E(ex，ey)满足ex < ax.
4. 计算线段AB在Y轴上投影长度为m,在X轴上的投影长度为n
5. 计算r = m/n,如果r > 1,视为向上滑动。
6. 同理计算线段AC,AD,AE在Y轴投影长度与X轴的投影长度之比，得出向右向下向左的滑动。

以上没考虑r为1的情况。

```html
<view>
  <button type="primary"  bindtouchstart="mytouchstart" bindtouchmove="mytouchmove">点我吧</button>
</view>
```

```javascript
Page({
  data: {
    //初始化touchstart坐标
    startPoint: [0,0]
  },
  mytouchstart: function(e){
    //开始触摸，获取触摸点坐标并放入数组中
    this.setData({startPoint: [e.touches[0].pageX, e.touches[0].pageY]});
  },
  //触摸点移动
  mytouchmove: function(e){
    //当前触摸点坐标
    var curPoint = [e.touches[0].pageX,e.touches[0].pageY];
    var startPoint = this.data.startPoint;
    //比较pageX值
    if(curPoint[0] <= startPoint[0]){
      if(Math.abs(curPoint[0] - startPoint[0]) >= Math.abs(curPoint[1] - startPoint[1])){
        console.log(e.timeStamp + '- touch left move')
      }else{
        if(curPoint[1] >= startPoint[1]){
          console.log(e.timeStamp + '- touch down move')
        }else{
          console.log(e.timeStamp + '- touch up move')
        }
      }
    }else{
      if(Math.abs(curPoint[0] - startPoint[0]) >= Math.abs(curPoint[1] - startPoint[1])){
        console.log(e.timeStamp + '- touch right move')
      }else{
        if(curPoint[1] >= startPoint[1]){
          console.log(e.timeStamp + '- touch down move')
        }else{
          console.log(e.timeStamp + '- touch up move')
        }
      }
    }
  }
})
```

#### 5.多点触控

由于模拟器尚不支持多点触控，内测开放后，继续补充。
> 注意
新版 微信小程序 `longtap` 事件已弃用 ,改用 `longpress`