# 简介

> [GSAP](https://greensock.com/)（GreenSock Animation Platform）是一个现代化的web动画库，创建DOM，SVG，Canvas动画。

# Demo

1. [gasp.to()的基本用法](https://codepen.io/GreenSock/pen/PoYjyNj)
2. 

# 安装

**npm**

```shell
npm i gsap -S
```

```js
import { gsap } from "gsap";
// 额外插件
import { CSSRulePlugin } from "gsap/CSSRulePlugin";
import { Draggable } from "gsap/Draggable";
import { EaselPlugin } from "gsap/EaselPlugin";
import { MotionPathPlugin } from "gsap/MotionPathPlugin";
import { PixiPlugin } from "gsap/PixiPlugin";
import { TextPlugin } from "gsap/TextPlugin";
import { ScrollToPlugin } from "gsap/ScrollToPlugin";
import { ScrollTrigger } from "gsap/ScrollTrigger";
// 注册插件
gsap.registerPlugin(CSSRulePlugin, Draggable, EaselPlugin, MotionPathPlugin, PixiPlugin, TextPlugin, ScrollToPlugin, ScrollTrigger);
```



**CDN**

```html
<!-- 核心包 -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.6.1/gsap.min.js"></script>
<!-- 插件 -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.6.1/CSSRulePlugin.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.6.1/Draggable.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.6.1/EaselPlugin.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.6.1/MotionPathPlugin.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.6.1/PixiPlugin.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.6.1/TextPlugin.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.6.1/ScrollToPlugin.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.6.1/ScrollTrigger.min.js"></script>
```



**下载**

[下载页面](https://greensock.com/docs/v3/Installation)



# GSAP

### 方法

#### tween（动画补间）

``` js
// 正向补间
gsap.to(".green", {duration: 3, rotation: 360, scale: 0.5});
// 反向补间
gsap.from("#logo", {duration: 3, x: 300, opacity: 0, scale: 0.5});
// 起止补间, duration写在第二组参数上
gsap.fromTo("#logo", {width: 0, height: 0}, {duration: 1.5, width: 100, height: 200});
// 时间线，详细说明参看时间线章节
var tl = gsap.timeline({repeat: 2, repeatDelay: 1});
tl.to("#id", {x: 100, duration: 1});
```
#### 默认设置

``` js
// tween的默认设置
gsap.defaults({ease:"power3.inOut"});
// 默认设置，不仅限于tween
gsap.config({
  autoSleep: 60, // 多少帧检查一次是否应该关闭动画，为设备省电，默认120，大约2秒
  force3D: false, // 是否启动gpu加速，使所有相关动画使用3d变换
  nullTargetWarn: false, // 默认情况，null目标元素将抛出错误，设为false，抑制此错误
  units: {left: "%", top: "%", rotation: "rad"} // 设置css属性值的默认单位
});
// dom属性的默认设置
gsap.set("#logo", {fontSize: 20, x: 10});
```
#### 获取

``` js
gsap.getById("tweenId"); // 获得指定id的tween

gsap.getProperty("#id", "x", "px"); // 获得指定id的tween的属性值，可指定单位
var getter = gsap.getProperty("#id"); // 只传入id返回一个getter函数。

gsap.getTweensOf("#id"|['#id1', '#id2']); // 获得节点上所有动画
```
#### 注册

```js
// 注册缓动曲线
gsap.registerEase("myEaseName", function(progress) {
    return progress; //linear
});

// 注册效果
gsap.registerEffect({
    name: "fade",
    effect: (targets, config) => {
        return gsap.to(targets, {duration: config.duration, opacity: 0});
    },
    defaults: {duration: 2},
    extendTimeline: true, // 设置为true时，可以在时间线的任意位置调用该效果
});
// 单个元素使用
gsap.effects.fade(".box");
// 在时间线上直接使用
let tl = gsap.timeline();
tl.fade(".box", {duration: 3})
  .fade(".box2", {duration: 1}, "+=2")
  .to(".box3", {x:100});

// 注册插件
gsap.registerPlugin(MotionPathPlugin, TextPlugin);
```
#### 其它

``` js
// 延迟执行函数
var delayedCall = gsap.delayedCall(1, myFunction, ["param1", "param2"]);
// 使用kill销毁
delayedCall.kill();

// 将松散的tween，timeline和delayCall，整合到一个根时间线上，然后可以统一暂停，继续，倒放
var tl = gsap.exportRoot();
gsap.to(tl, {duration: 0.5, timeScale: 0});
// 这个动画不会执行，因为他在exportRoot后创建的
gsap.fromTo(myWindow, {scaleX: 0, scaleY: 0}, {duration: 1, scaleX: 1, scaleY: 1});

gsap.isTweening("#id"); // 动画是否正在执行中。paused，completed，或者还没开始，都返回false。

gsap.killTweensOf(".myClass"); // 销毁指定节点上所有动画。
gsap.killTweensOf(myObject, "opacity,x"); // 也可指定个别属性

gsap.parseEase("power1"); // 传入一个ease字符串，返回一个ease解析函数

// quickSetter。当需要频繁使用gsap.set()时，quickSetter将节省150%性能。接收3个参数，节点类名，属性名，属性值单位。
// demo链接：鼠标跟随效果：https://codepen.io/GreenSock/pen/WNNNBpo
let xSetter = gsap.utils.pipe(
    gsap.utils.clamp(0, 100),    // 限制数字在0~100之间
    gsap.utils.snap(5),          // 吸附数字接近5的倍数
    gsap.quickSetter("#id", "x", "px") // 传入类名，属性名，属性值单位参数
);
xSetter(150); // 为#id设置x: 100px属性（被clamp收紧）
xSetter(3); // 为#id设置x: 5px属性（被snap吸附）

// 手动更新根节点。通常gasp在内部使用requestAnimationFrame自动更新根节点，但是专业游戏开发者有时需要自己更新。
gsap.updateRoot()
```

### 属性

#### gsap.effects

```js
// 包含所有 gsap.registerEffect() 注册的效果，为元素添加effect
gsap.effects.effectName(dom, {...config})
```

#### gsap.globalTimeline

```js
// 根时间线实例
// 常用方法
gsap.globalTimeline.pause(); // 暂停
gsap.globalTimeline.play(); // 播放
gsap.globalTimeline.paused(); // 暂停状态
gsap.globalTimeline.timeScale(number?); // 倍速播放。如不传值，则获取当前根时间线播放速度。
```

#### gsap.ticker

```js
// ticker类似于gsap引擎的心跳-它在每个requestAnimationFrame事件上更新globalTimeline，因此它与浏览器的呈现周期完全同步。您可以添加自己的侦听器，以便在每次更新后运行自定义逻辑（对于游戏开发人员来说非常好）。添加任意多个侦听器。

gsap.ticker.add(myFunction); // 添加监听，每次心跳都执行
gsap.ticker.remove(myFunction); // 移除监听
// 监听函数接收三个参数
// time: ticker启动以来的总时长（秒）,
// deltaTime: 间隔上一次心跳的毫秒数，也就是一帧用时
// frame: 帧编号，每次心跳自增
function myFunction(time, deltaTime, frame) {
  // 这里的代码动画每一帧都会执行一次
}

gsap.ticker.fps(30); // 将每秒帧数限制为30

gsap.ticker.deltaRatio(60); // 帧数比。如果设置为60，而实际帧数为30，返回2
gsap.ticker.add(function() {
  obj.x += 3 * gsap.ticker.deltaRatio(60); // 浏览器帧数波动的情况下仍返回相同结果
});
// 默认的fps参数是60，因此您甚至不需要传入一个参数，除非您使用的是60fps以外的其他参数。例如，如果您想基于以30fps速度运行的事物来获取比率，那么可以使用gsap.ticker.deltaRatio(30)
```

#### gsap.utils

> 工具函数

```js
checkPrefix(cssProp); // 传css属性名，返回带有浏览器前缀的版本，如果不需要前缀，返回自身，如果属性不存在，返回undefined
```
```js
clamp(minimum, maximum, valueToClamp); // 收紧数字。给定最小值和最大值，如果传入的值小于最小值，返回最小值；如果传入的值大于最大值，返回最大值。如果传入的值在最小值和最大值之间，返回自身。
clamp(minimum, maximum); // 返回一个函数，函数的参数为要处理的值。
```
```js
// 返回一个函数，根据传入的值分配一个数组的值
distribute({
  base: number, // 起始值，默认0
  amount: number, // 分配的总数
  from: [number|string|array], // 目标数组中要开始的位置。默认0，可以是位置字符串"start", "center", "edges", "random", "end"。也可以是一个数组[0.25, 0.75]
  grid: [string|array], // 基于元素在网格[行，列]中的位置的分布，如[5，10]。可以使用“auto”自动检测位置
  axis: string, // 对于基于网格的分布，可以将测量限制为一个轴（“x”或“y”）。
  ease: Ease // 基于缓动曲线分布，默认“none”
});
```
```js
getUnit(string); // 获得单位，"50%"返回"%", "100vw"返回"vw"
```
```js
interpolate(startValue, endValue, progress); // 一种超级灵活的方法，可以在任何两个类似类型的值（数字、颜色、字符串、数组、具有多组嵌入数字的复杂字符串、具有多个属性的对象……几乎任何东西）之间进行线性插值。提供介于0和1之间的进度值，提供0.5时，将返回起止的中间值。(0, 500, 0.5) => 250, ("20px", "40px", 0.5) => "30px", ("red", "blue", 0.5), ({a:0, b:10, c:"red"}, {a:100, b:20, c:"blue"}, 0.5) => {a: 50, b: 15, c: "rgba(128,0,128,1)"}
interpolate(array, progress); // 第一个参数为进度数组，第二个参数为进度值。([100, 50, 500], 0.75) => 275
interpolate(startValue, endValue); // 返回一个函数。这个函数接收一个进度值参数。
interpolate(array); // 返回一个函数。这个函数接收一个进度值参数。
```
```js
mapRange(inMin, inMax, outMin, outMax, valueToMap); // 将数字在一个范围内的相对位置映射到另一个范围内的等效位置。例如，给定0到100的范围，50是中间值，因此如果映射到0到500的范围，它将是250（因为这是该范围的中间值）。把它想象成比率。(-10, 10, 100, 200, 0) => 150
mapRange(inMin, inMax, outMin, outMax); // 返回一个函数。函数接收一个值作为参数
```
```js
normalize(minimum, maximum, valueToNormalize); // 第三个参数在起止值之间的比率。简单地理解就是 (v-min)/(max-min)
normalize(minimum, maximum); // 返回一个函数。函数接收一个值作为参数
```
```js
pipe(func1, func2, func3); // 管道。将多个函数串联在一起，将上一个函数的返回值作为参数传给下一个。
// var transfrom = gsap.utils.pipe(func1, func2, func3);
// var output = transform(input);
// 等效↓
// var output = func1(func2(func3(input)));
```
```js
random(minimum, maximum[, snapIncrement, returnFunction]); // 返回一个范围内的随机值。snapIncrement：指定增量。returnFunction：是否返回函数，将其赋值给一个变量，每次调用这个变量来重复使用。
random(array[, returnFunction]); // 返回数组的随机项
random(minimum, maximum[, returnFunction]); // 随机数
// 作为字符串使用
// gsap.to(".class", { x:"random(-100, 100, 5)" });
```
```js
shuffle(array); // 洗牌。打乱数组
```
```js
snap(snapIncrement, valueToSnap); // 捕捉到距离这个增量最近的值。(10, 23.5) => 20, (2, 9.3) => 10
snap(snapIncrement); // 返回一个函数，函数接收一个参数
snap(array, valueToSnap); // 选择最接近数组中的一个值
snap(array); // 返回一个函数，函数接收一个参数
snap(objectWithRadius, valueToSnap); // 参数为{[number|{x,y}], radius}或者{increment, radius}
// 返回30.5。因为半径20内没有可选值
// gsap.utils.snap({values:[0, 100, 300], radius:20}, 30.5); 
// 获取最接近point的点
// var point = {x:8, y:8};
// gsap.utils.snap({values:[{x:0, y:0}, {x:10, y:10}, {x:20, y:20}], radius:5}, point);
snap(objectWithRadius); // 返回一个函数，作用同上
```
```js
splitColor(string[, returnHSL]); // 将颜色字符串转换成[r, g, b]格式，如果有透明度，则为[r, g, b, a]。额外参数可指定hsla格式
```
```js
toArray(selector|dom|jQueryDom); // 将选择器文本、对象数组或选择器文本、节点列表、对象或几乎任何类似数组的对象（如jQuery结果）转换为平面数组。
```
```js
unitize(function, unit); // 将参数返回值加上单位
```
```js
wrap(array, index); // 包裹。即使下标超出数值长度，也能继续从头开始计数，最终找到哪个值。
wrap(min, max, index); // 指定一个范围
wrap(array); // 返回一个函数，这个函数接收index
```
```js
wrapYoyo(); // 同wrap，不同的是，超出范围不是从头，而是从尾部倒数。
```

#### gsap.version

> 返回当前版本，如"3.6.1"



# Tween

## 创建动画

```js
let tween = gsap.to(".className", {
  // 特殊参数
  id: 'myTween', // 唯一标识符，后面可以使用gsap.getById()获取它
  duration: 3, // 持续时间/s，默认0.5
  delay: 1, // 延迟播放时间/s
  repeat: 1, // 重复次数。-1无数次。默认0。
  repeatDelay: 3, // 重复播放间隔/s。默认0。
  repeatRefresh: true, // 每次重复是否刷新数据。使用动态值时很有用，比如 x: "random(-100, 100)"，每次重复会重新计算值，而不是只用第一次得到的随机值。duration, delay, stagger不会被刷新。
  ease: 'ease', // 缓动曲线，默认'none'线性
  stagger: 0.2, // 错开时间。如果定义多个目标，将错开每个目标的开始时间。也可以设置对象来获得更高级的错位效果。
  stagger: { // 高级stagger object配置
    amount: 5, // 所有参差中分配的总时间
    each: 0.1, // 相邻两个参差时间间隔。和amount二选一使用。
    from: "center", // String|Number|Array。设置为数字时，指定从数组的哪个位置开始，4代表第5个。还可以使用字符串"start", "center", "edges", "random", 或 "end"，如果定义了grid，还可以使用数组，[0.5, 0.5]表示中心，[1, 0]表示右上角。
    grid: "auto", // Array|"auto"。手动指示网格的行列数，如[9, 5]。或者设置“auto”自动获取。
    axis: "x", // "x"|"y"|false。如果定义了grid。此参数代表变换的坐标轴。定义x代表二维网格按照横向变换，每列将同步变换。
    ease: "power2.inOut", // 缓动曲线
    repeat: -1 // 重复次数。-1代表无限重复。
  },
  startAt: {x: 100}, // 定义属性的起始值。和gsap.set()效果相同。
  paused: true, // 是否暂停，默认false。如果为true，默认不播放，使用tween.play()触发播放。
  immediateRender: false, // 立即渲染。to默认是false，from/fromTo/scrollTringger默认是true
  data: string|object|array|etc., // 将任意数据赋值给这个属性，然后通过实例tween.data使用
  inherit: false, // 通常，动画会继承父时间线的defaults设置的属性，可以通过inherit: false来禁用继承
  lazy: false, // 延迟属性写入。默认true，通常不需要设置
  overwrite: true, // 覆盖规则。如果是true，所有绑定相同dom的其他tween将被立刻销毁。如果是 "auto"，则只会覆盖那些属性冲突的部分。默认false。
  reversed: true, // 如果是true，动画将朝着时间起点反向开始。默认false。
  runBackwards: true, // 如果是true，将反转起始值和结束值。简单的说，就是会把to变成from。
  yoyo: true, // 悠悠球效果。重复时将会从结束点回到起始点。
  yoyoEase: 'power2.inOut', // 悠悠球缓动效果。
  keyframes: [{x: 100, duration: 1}, {y: 100, duration: 1}], // 如果要设置一系列动画，可以使用keyframes。
  
  // css参数 - transform相关
  x: 100, // transform: translateX(100px)
  y: 100, // transform: translateY(100px)
  xPercent: -50, // transform: translateX(-50%)
  yPercent: -50, // transform: translateY(-50%)
  rotation: 360, // transform: rotate(360deg)
  rotationX: 360, // transform: rotateX(360deg)
  rotationY: 360, // transform: rotateY(360deg)
  skewX: 45, // transform: skewX(45deg)
  skewY: 45, // transform: skewY(45deg)
  scale: 2, // transform: scale(2, 2)
  scaleX: 2, // transform: scaleX(2)
  scaleY: 2, // transform: scaleY(2)
    
  // css参数 - 非transform
  width: 100,
  height: 100,
  opacity: 0.5,
  backgroundColor: 'red',
  fontSize: 18,
  left: 100,
  top: 100,
  right: 100,
  bottom: 100,
  // ...
    
  // 回调函数
  onComplete: callbackFn, // 完成回调
  onCompleteParams: ['complete message'], // 作为回调函数的message参数传入
  onStart: callbackFn, // 开始回调
  onStartParams: ['start message'],
  onRepeat: callbackFn, // 重复回调
  onRepeatParams: ['repeat message'],
  onUpdate: callbackFn, // 更新回调
  onUpdateParams: ['update message'],
  onReverseComplete: callbackFn, // 倒放回调起点回调
  onReverseCompleteParams: ['reverse complete message'],
  onInterrupt: callbackFn, // 动画被打断。动画执行中被kill销毁触发。
  onInterruptParams: ['Interrupt message'],
  callbackScope: scope, // 作用域。将作用于所有回调函数
    
  // CSS Plugin
  autoAlpha: 0, // 和opacity类似，但是有两点区别。当autoAlpha变为0时，visibility也为hidden，并禁止交互。当最初设置visibility: hidden, autoAlpha: 1 时，它将假定alpha也从0开始。使淡入淡出变得简单。
  clearProps: "scale,left", // 设置一个逗号分割的属性列表，在动画播放结束时清除。transform相关的将被一起清除。
  autoRound: false, // 自动取整，默认true。为了节省性能，动画过程中默认将小数转换成接近的整数。
});
```



## 实例属性

```js
data; // 存储数据的地方。如果初始化时定义了data，则会自动填充
ratio; // 只读。动画的ease进程。例如power2.out, 0.5秒tween.progress()返回0.5，而tween.ratio返回0.875。
vars; // 初始化时传入的参数。
```



## 实例方法

**获取或设置**

```js
delay([number]); // 获取或设置动画的初始延迟
duration([number]); // 获取或设置动画的持续时间
startTime([Number]); // 获取或设置动画在父时间线上的开始时间。
eventCallback("onComplete"[, myFunction, ["param1","param2"]]); // 获取或设置回调函数
iteration([Number]); // 获取或设置当前迭代次数。
repeat([Number]); // 获取或设置重复次数。
repeatDelay([Number]); // 获取或设置重复间隔。
paused([Boolean]); // 获取或设置暂停状态。
reversed([Boolean]); // 获取或设置倒放状态。
time([Number, suppressEvents]); // 获取或设置播放头的时间点。
totalDuration([Number]); // 获取或设置总持续时间。包括重复和重复延迟。
totalProgress([Number, suppressEvents]); // 获取或设置总进度。范围在0~1之间。 
totalTime([Number, suppressEvents]); // 获取或设置播放头的位置，根据totalDuration。
yoyo([Boolean]); // 获取或设置yoyo状态。
```

**播放控制**

```js
play([Number, suppressEvents]); // 播放。传入时间将跳到精确的秒数再开始。
pause([Number, suppressEvents]); // 暂停。传入时间将跳到精确的秒数再暂停。
resume(); // 继续
reverse([time, suppressEvents]); // 倒放。time可以是正数(距离起点)、0(终点)、负数(距离终点)和label。
restart([includeDelay, suppressEvents]); // 重播。includeDelay:是否包含延迟时间，默认false。
seek(time[, suppressEvents]); // 跳转。time可以是精确的时间或者label。
progress([Number, suppressEvents]); // 获取或设置播放进度。0.5代表跳到50%。
timeScale(Number); // 倍速播放
```

**其他**

```js
endTime([includeRepeats]); // 返回动画的结束时间。接收一个参数，说明是否包含重复播放的时间。
invalidate(); // 刷新任何内部记录的开始/结束值，如果要重新启动动画而不还原到任何以前记录的开始值，则该值非常有用。
isActive(); // 是否活动状态。未开始，暂停，播放结束，父时间线暂停，都将返回false。
kill([Object|Array(), properties]); // 根据参数进行部分或完全销毁。销毁将从父时间线移除，并进行垃圾回收。
targets(); // 获取目标元素数组。如果传入的是class，返回的是DOM数组。
then(fn).then(fn2); // 执行一个回调函数，返回一个Promise，常用来代替onComplete。注意，这两个then的含义不同。
```



## 缓动曲线

> 参考链接：[可视化缓动编辑器](https://greensock.com/docs/v3/Eases)

```js
gsap.to(dom, { duration: 2.5, ease: "none", y: -500 });
```

| 名称                                           | 参数                                                         | 描述                                                         |
| ---------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| none \| linear \| power0                       |                                                              | 默认值，无缓动                                               |
| power1.in                                      |                                                              | 慢入                                                         |
| power1.out                                     |                                                              | 慢出                                                         |
| power1.inOut                                   |                                                              | 慢入慢出                                                     |
| // power2/3/4                                  |                                                              | 相比power1缓动程度逐级递增                                   |
| back.in(1)                                     | 可选，默认1，1~4                                             | 先回弹，再进入                                               |
| back.out(1)                                    | 可选，默认1，1~4                                             | in 的反向                                                    |
| back.inOut(1)                                  | 可选，默认1，1~4                                             | in + out 的效果                                              |
| elastic.in                                     |                                                              | out 的反向                                                   |
| elastic.out                                    |                                                              | 橡皮筋效果                                                   |
| elastic.inOut                                  |                                                              | in + out 的效果                                              |
| bounce.in                                      |                                                              | out 的反向                                                   |
| bounce.out                                     |                                                              | 弹球效果                                                     |
| bounce.inOut                                   |                                                              | in + out 的效果                                              |
| slow(0.1, 0.1, false) *额外                    | 参数1：起止倾斜程度，0~1；0是45度，1是90度。<br>参数2：中间的倾斜程度，0~2，0是45度，1是0度，2是-45度。<br>参数3：是否镜像 | 调整两端和中间斜率，并且可镜像                               |
| steps(20)                                      | 步进次数，1~100                                              | 步进效果                                                     |
| circ.in                                        |                                                              | 慢入快出，曲线是个1/4圆弧                                    |
| circ.out                                       |                                                              | 快入慢出                                                     |
| circ.inOut                                     |                                                              | 两头慢中间快                                                 |
| expo.in                                        |                                                              | 慢入快出，比circ程度更深                                     |
| expo.out                                       |                                                              | in 的反向                                                    |
| expo.inOut                                     |                                                              | in + out 的效果                                              |
| sine.in                                        |                                                              | 更柔和的慢入快出                                             |
| sine.out                                       |                                                              | 更柔和的快入慢出                                             |
| sine.inOut                                     |                                                              | in + out 的效果                                              |
| rough({}) *额外                                |                                                              | 疯狂无序的动画。*参数见下详解*                               |
| customEase.create('custom', 'M0,0,C1,1') *额外 |                                                              | 自定义，需要额外引入文件[greensock.com/customease](https://greensock.com/customease/) |

**rough的用法**

```js
gsap.to(dom, { duration: 2.5, ease: "rough({ template: none, strength: 1, points: 20, taper: none, randomize: true, clamp: false })", y: -500 });
```

- template：基于什么缓动模板，可选值为ease可选值
- strength：混乱强度，0~2
- points：混乱点数，10~200
- taper：平缓区域，可选值：none，in，out，both
- randomize：是否随机，设置false时，为有规律地抖动
- clamp：是否夹紧，设置为true时，缓动曲线不会超出方框

**相似类型缓动的程度排序**

以下缓动有相似的缓动效果，都有 in / out / inOut 属性，按照程度从小到大排序为

```js
sine < power1 < power2 < power3 < power4 < circ < expo
```



# Timeline

> 参考链接：[https://greensock.com/docs/v3/GSAP/Timeline](https://greensock.com/docs/v3/GSAP/Timeline)

## 创建时间线

```js
// 创建时间线
var tl = gsap.timeline({
  // 时间线属性
  repeat: 2, // 时间线重复次数
  repeatDelay: 1, // 时间线重复间隔
  repeatRefresh: true, // 是否每次迭代完成后，重设状态
  yoyo: true, // 是否悠悠球式重复，为true时，动画重复播放时展示为A-B-B-A，否则默认为A-B-A-B
  delay: 2, // 时间线延迟时间
  autoRemoveChildren: true, // 是否在子时间线播放完成后删除。
  paused: false, // 暂停状态
  smoothChildTiming: false, // 是否自动重新定位子动画（更改其startTime），以便在动态更改计时相关属性时保持平滑播放。
  // 动画默认属性
  defaults: {
    duration: 1,
    ease: 'elastic'
  },
  // 回调函数属性
  onComplete: callbackFn, // 完成回调
  onCompleteParams: ['complete message'], // 作为回调函数的message参数传入
  onStart: callbackFn, // 开始回调
  onStartParams: ['start message'],
  onRepeat: callbackFn, // 重复回调
  onRepeatParams: ['repeat message'],
  onUpdate: callbackFn, // 更新回调
  onUpdateParams: ['update message'],
  onReverseComplete: callbackFn, // 倒放回调起点回调
  onReverseCompleteParams: ['reverse complete message'],
  callbackScope: obj, // 指定回调函数的作用域
});

// 在时间线上安插动画
tl.to("#id", {x: 100, duration: 1});
tl.addLabel("spin", 3); // 在时间线的3秒处添加一个标签
tl.from("#id", {opacity: 0, duration: 1}, "spin");
```



## 实例属性

```js
tl.vars; // 包含了所有创建时间线时设置的参数
tl.data; // 用来存储数据，默认使用vars.data填充，如果存在的话
tl.labels; // 储存了所有被添加到时间线的标签
tl.parent; // 时间线的父级
tl.smoothChildTiming; // 布尔值。控制是否自动重新定位子时间线和时间线（更改其开始时间），以便在动态更改属性时保持平滑播放。
tl.autoRemoveChildren; // 布尔值。控制是否在子时间线播放完成后删除。
```



## 实例方法

```js
// 字符说明：
// time: 时间参数
// suppressEvents: 阻止回调函数，布尔值
// dom: css选择器字符串，dom对象，或者以上两者的数组
// params: 参数
// ?: 可选

// 动画
tl.to(dom, {...params}, time); // 在时间线上添加正向补间
tl.from(dom, {...params}, time); // 在时间线上添加反向补间
tl.fromTo(dom, {...params}, {...params}, time); // 在时间线上添加起止补间
tl.tweenTo(time, {...params}); // 创建一个到达指定时间的动画。如果时间在当前时间之前，则会倒放，但是reversed还是false。tweenTo() 会暂停当前时间线，并且播放完成后不会恢复。想要继续播放，在onComplete里调用resume()
tl.tweenFromTo(time, time, {...params}); // 创建一个线性tween，基本上从一个特定的时间或标签到另一个时间或标签，然后停止播放头。

// 设置属性
tl.set(dom, {...params}, time); // 在时间线尾部添加一个属性更改，相当于add( gsap.to(target, {duration: 0, ...}) )

// 获取或设置
tl.delay(number?); // 获取或设置动画的初始延迟
tl.duration(number?); // 获取或设置动画的持续时间
tl.startTime(number?); // 获取或设置在其父线上的开始时间
tl.iteration(number?); // 获取或设置时间线的迭代次数。
tl.repeat(number?); // 获取或设置时间线在第一次迭代后应重复的次数。设置为1，时间线播放两次。无限重复，应设置为-1
tl.repeatDelay(number?); // 获取或设置每次重复间隔
tl.time(number?, suppressEvents?); // 获取或设置播放头的本地位置（基本上是当前时间），不包括任何重复或重复延迟。
tl.paused(isPaused?); // 获取或设置动画的暂停状态，该状态指示当前动画是否暂停
tl.reversed(isReversed?); // 获取或设置动画的倒放状态
tl.totalDuration(number?); // 获取或设置时间线的总持续时间，包含延迟和重复。
tl.totalProgress(number?, suppressEvents?); // 获取或设置时间线的总进度，该进度值介于0和1之间，表示虚拟播放头（包括重复播放）的位置，其中0在开始处，0.5在中间点，1在结束处（完成）。
tl.yoyo(isYoyo?); // 获取或设置悠悠球状态。为true时，动画重复播放时展示为A-B-B-A，否则则默认为A-B-A-B

// 仅获取
tl.endTime(isIncludeRepeats?); // 获取时间线的结束时间，如果传入true，则把重复次数计算在内
tl.currentLabel(); // 获取当前位置最近的label
tl.previousLabel(time); // 获取上一个标签，传入时间，则以那个时间为起点获取
tl.nextLabel(time); // 获取下一个标签，传入时间，则以那个时间为起点获取
tl.getById(id); // 通过id获取动画
tl.getChildren(isNested?, isIncludeTween?, isIncludeTimeline?, ignoreBeforeTime?); // 获取当前时间线所有子时间线
tl.getTweensOf(dom, isNested?); // 获取当前时间线此节点上所有动画，默认包含嵌套 
tl.isActive(); // 获取是时间线活动状态
tl.recent(); // 获取最近添加的子时间线(add)/动画(to...)/回调函数(call...)

// 添加
tl.add(childTl, time); // 添加子时间线
tl.addLabel('labelName', time); // 添加标签，供to和from使用
tl.addPause(time); // 添加暂停

// 回调相关
tl.call(fn, {...params}, time); // 添加回调到时间线末尾，或者使用时间参数控制位置
tl.eventCallback(type, fn, {...params}); // 比call多传一个事件类型，onComplete等
tl.then(fn).then(fn2); // 执行一个回调函数，返回一个Promise，常用来代替onComplete。注意，这两个then的含义不同。

// 清除
tl.remove(tween || [tween, childTl, 'label']); // 移除指定的动画，子时间线，标签，回调函数
tl.removeLabel('label'); // 移除指定标签
tl.removePause(pause); // 移除指定暂停
tl.clear(); // 清除时间线的动画、子时间线、回调函数、标签等
tl.kill(); // 销毁时间线

// 播放控制
tl.play(); // 播放
tl.pause(); // 暂停
tl.resume(); // 继续
tl.reverse(from?, suppressEvents?); // 倒放, from是倒放的位置，默认当前播放头位置，可传label，0是动画结尾，-1是结尾1秒
tl.restart(includeDelay?, suppressEvents?); // 重播，includeDelay：是否包含延时
tl.seek(time, suppressEvents?); // 跳转。
tl.progress(0~1, suppressEvents?); // 跳到n%
tl.timeScale(0~∞); // 倍速播放

// 时间线操作
tl.invalidate(); // 刷新初始状态为当前状态，当重复播放时，不再回到最初点，而是以新设置的状态为起点
tl.shiftChildren(amount, adjustLabels, ignoreBeforeTime); // 移动子时间线的时间
```



## 时间参数

> 时间线实例的 to 和 from 方法的第三个参数为时间参数，指定动画的播放时间。
>
> 其他方法也会用到时间参数，如addLabel，add，addPause，call

```js
tl.to(dom, {...params}, time);

// time包含以下情况
3 // 在3秒处插入
"+=1" // 在时间线结束后1秒插入
"-=1" // 在时间线结束前1秒插入
"someLabel" // 在标签处插入动画
"someLabel+=2" // 在标签处2秒后插入
"someLabel-=2" // 在标签处2秒前插入
"<" // 在最近添加的动画的开头插入
">" // 在最近添加的动画的结尾插入
"<1" // 在最近添加的动画的开头1秒后插入
"<-1" // 在最近添加的动画的开头1前插入
">1" // 在最近添加的动画的结尾1秒后插入
">-1" // 在最近添加的动画的结尾1前插入
```



## 子时间线

```js
function intro() {
	var tl = gsap.timeline();
	//...在这添加动画...
	return tl;
}

function middle() {
	var tl = gsap.timeline();
	//...在这添加动画...
	return tl;
}

function conclusion() {
	var tl = gsap.timeline();
	//...在这添加动画...
	return tl;
}

// 将它们缝合在主时间线上...
var master = gsap.timeline();
master.add(intro())
      .add(middle(), "+=2")     // 与intro间隔2秒
      .add(conclusion(), "-=1") // middle结束前1秒添加到时间线
```



# 插件

## 核心(内置)插件

> 核心插件在结构上类似于GSAP的插件，但包含在GSAP的核心中，不必使用GSAP.registerPlugin（）加载。你可以把它们看作GSAP的一部分。

**AttrPlugin**

> 使用 attr:{} 参数，为dom的属性添加动画

```html
<rect id="rect" fill="none" x="0" y="0" width="500" height="400"></rect>
```

```js
gsap.to("#rect", {duration: 1, attr: {x:100, y: 50, width: 100, height: 100}, ease: "none"});
```

AttrPlugin 不参与 CSS 相关的属性设置，因为CSSPlugin会处理这些，如果上面的例子 x 写在外侧，将被CSSPlugin转换成 transformX。

**CSSPlugin**

> 为所有 CSS 相关的属性添加动画。
>
> 注意：css 里的包含分隔符的属性，要转换为驼峰式写法。如 ”font-size“ 应写成 "fontSize"
>
> CSSPlugin 默认单位是 ”px“，所以当你想设置 x: "20px" 时，可以简写为 x: 20
>
> rotation、rotationX、rotationY、skewX、skewY 默认单位是 deg，你可以为它显式加上单位 deg 或 rad。180deg = π rad

css 变量  [css变量变色demo](https://codepen.io/GreenSock/pen/MoeLdj)

**EndArrayPlugin**

这个插件可以将一个数组转换成另一个数组，以补间动画的形式。

如果数组长度不等，则只有共同拥有的索引才会变化。

```js
const arr = [1, 2, 3];

gsap.to(arr, {
  endArray: [5, 6, 7],
  onUpdate() {
    console.log(arr)
  }
});
```

**ModifiersPlugin**

创建动画时传入 modifiers 参数，参数的key 是 动画属性名，值是函数，函数接收两个参数，value，当前属性值；target，当前对象。

[ModifiersPlugin Demo: 将旋转一周修饰为每45度旋转一次](https://codepen.io/GreenSock/pen/BzJxBB)

[轮播图滚动](https://codepen.io/GreenSock/pen/QEdpLe/)

[Modifier合辑](https://codepen.io/collection/AWxOyk/)

**SnapPlugin**

捕捉任何属性到设置的snap。snap的值接收以下几种参数

Number: 捕捉到这个值最接近的倍数。

Array: 从数组中选择最接近的值。

Object: {values: Array, radius: Number} 。以radius为半径，捕捉



## 官方插件

> 除了核心插件，其他插件都需要注册，例如：

```js
gsap.registerPlugin(MotionPathPlugin, ScrollTrigger, MorphSVGPlugin);
```



### ScrollTrigger

> 创建滚动动画或触发与滚动相关的事件。
> 支持DOM, CSS, SVG, WebGL, Canvas。
> 支持反向滚动倒放。
> 支持水平和垂直滚动。
> 支持跟踪速度。

**简单的例子**

```js
gsap.to(".box", {
  scrollTrigger: ".box", // 在.box出现在视窗里时，动画开始
  x: 500
});
```

**高级的例子**（完整参数）

```js
let tl = gsap.timeline({
    // 是的，你可以把它安插在时间线上
    scrollTrigger: {
      trigger: ".container", // String|Dom, 起始位置
      pin: true, // Boolean|String|Dom, 激活时锁定触发元素。其余内容滚动时，它将钉住在页面上。
      start: "top top", // String|Number|Function  确定触发器和滚动器达到某个位置时触发。可以是位置名称"top top"代表触发器的顶部到达滚动器的顶部时触发。也可以是"100px" "80%" 或者 “top bottom-=100px”。当是Number时，200代表滚动器滚动200像素时触发。
      end: "+=500", // String|Number|Function。 滚动的结束位置
      scrub: 1, // Boolean|Number。过度时间。设置为true，动画将与滚动条同步。设置数字，代表动画追上滚动条所用的时间。
      snap: 0.1, // Number|Array|Function|Object|'labels'|'labelsDirectional'。停止滚动后捕捉到某个位置
      snap: {
        snapTo: "labels", // Number|Array|Function|"labels" 必选。吸附逻辑
        duration: {min: 0.2, max: 3}, // Number|Object 吸附动画持续时间范围，根据滚动速度而定
        delay: 0.2, // 从上次滚动事件开始等待0.2秒，然后再执行捕捉
        ease: "power1.inOut" // 缓动动画，默认值power3,
        inertia: false, // Boolean 惯性。默认true
        onStart: callBack, // 吸附开始回调
        onInterrupt: callBack, // 吸附中断回调
        onComplete: callBack, // 吸附完成回调
      },
      horizontal: true, // 是否水平滚动，默认false
      animation: tween|timeline, // 动画
      id: String, // 唯一标识符
      // 事件回调
      onEnter: callBack,
      onEnterBack: callBack,
      onLeave: callBack,
      onLeaveBack: callBack,
      onRefresh: callBack,
      onUpdate: callBack,
      onScrubComplete: callBack,
      onSnapComplete: callBack,
      onToggle: callBack,
      // 不常用
      once: true, // 默认false。是否只触发一次。当true时，一旦到达结束位置一次，scrollTrigger就会被回收
      markers: Object|Boolean, // 添加开发或排除时的标记点。当true时，默认参数为{startColor: "green", endColor: "red", fontSize: "16px", fontWeigth: "normal", indent: 0}, 可以传一个相同格式的对象自定义它
      invalidateOnRefresh: true, // 默认false。如果是true，将在refresh()时调用invalidate(),通常是窗口尺寸变化时
      endTrigger: String|Element, // 通常不需要定义，除非endTrigger和trigger不同 
      anticipatePin: 1, // Number, 默认0。 预测钉住。通常用来解决钉住的元素的闪烁。
      refreshPriority: 1, //Number。刷新优先级。默认按照文档流顺序。除非你想改变执行顺序，才用到这个属性。
      pinContainer: '.parentContainer', // String|Dom,当处于另一个scrollTrigger里时，父级元素
      pinReparent: true, // Boolean, 固定元素是否脱离父级元素，重新渲染到body元素下。
      pinSpacing: true, // Boolean, 间距
      pinType: "fixed", // 默认情况，body是滚动条时，是fixed，其他情况是transform。
      scroller: ".scroller", // String|Dom，默认情况滚动条是viewport本身，也可以自定义。
      toggleActions: "play pause resume reset", // 各阶段的动画播放状态。对应onEnter, onLeave, onEnterBack, onLeaveBack四个过程
      toggleClass: 'active', // 活动状态的class名称。如果仅对部分触发器可以传入对象，toggleClass: {targets: ".my-selector", className: "active"} 
    }
  });

// 在时间线上添加动画和标签
tl.addLabel("start")
  .from(".box p", {scale: 0.3, rotation:45, autoAlpha: 0})
  .addLabel("color")
  .from(".box", {backgroundColor: "#28a92b"})
  .addLabel("spin")
  .to(".box", {rotation: 360})
  .addLabel("end");
```

**独立/自定义使用**

```js
ScrollTrigger.create({
  trigger: "#id",
  start: "top top",
  endTrigger: "#otherID",
  end: "bottom 50%+=100px",
  onToggle: self => console.log("toggled, isActive:", self.isActive),
  onUpdate: self => {
    console.log("progress:", self.progress.toFixed(3), "direction:", self.direction, "velocity", self.getVelocity());
  }
});
```

**方法**

[ScrollTrigger方法](https://greensock.com/docs/v3/Plugins/ScrollTrigger/static.addEventListener())

**Demo**

[ScrollTrigger 官方demo 合辑](https://greensock.com/st-demos/)

[codepen ScrollTrigger How-To Pen](https://codepen.io/collection/AEbkkJ)

[codepen ScrollTrigger 网友作品展厅](https://codepen.io/collection/DkvGzg)

[ScrollTrigger最常犯的错误](https://greensock.com/st-mistakes/)

**理解ScrollTrigger的Demo**

[简单的demo理解ScrollTrigger](https://codepen.io/GreenSock/pen/RwPXQOQ)

[基础滚动动画](https://codepen.io/GreenSock/pen/abOPrBj)

[事件切换](https://codepen.io/GreenSock/pen/LYVKWGo)

[scrub绑定滚动条](https://codepen.io/GreenSock/pen/WNvVOWw)

[不同的scrub值对比](https://codepen.io/mikeK/pen/210c472fee9a62da501605794ffd6e10)

[pin固定](https://codepen.io/GreenSock/pen/YzXdbQo)

[事件回调](https://codepen.io/GreenSock/pen/qBdeVJY)

**全屏导览**

[全屏覆盖上一个部分](https://codepen.io/GreenSock/pen/KKpLdWW)

[全屏覆盖下一个部分，滚动揭开](https://codepen.io/GreenSock/pen/BaowPwo)

[无限循环滚动，切换过渡动画](https://codepen.io/GreenSock/pen/rNOebyo)

[横向滚动吸附的简单例子](https://codepen.io/GreenSock/pen/YzygYvM)

[独立的横向滚动部分](https://codepen.io/GreenSock/pen/eYpOZvX)

**视差效果**

[简单视差效果](https://codepen.io/GreenSock/pen/JjYPQpN)

[视差头部](https://codepen.io/GreenSock/pen/OJyPmgX)

[视差章节](https://codepen.io/GreenSock/pen/QWjjYEw)

**锚链接**

[简单锚链接](https://codepen.io/GreenSock/pen/bGVjLwG)

**其他各种效果**

[AriPods光影变化](https://codepen.io/osublake/pen/2152a28cffe2c2c0cca8a3e47f7b21c6?editors=0010)

[滚动组装一双鞋](https://codepen.io/GreenSock/pen/gOabMXv)

[滚动拆解一把手枪](https://codepen.io/GreenSock/pen/MWaWPmG)

[向下滚向上滚不同动画效果](https://codepen.io/GreenSock/pen/pojzxwZ)

[滚动时绘制svg路径](https://codepen.io/GreenSock/pen/rNOBLBV)

[滚动无限加载](https://codepen.io/kdbkapsere/pen/WNQmvPK)

[固定的元素在滚动时播放动画](https://codepen.io/GreenSock/pen/gOaOeGz?editors=0100)

[滚动时增强动画幅度](https://codepen.io/GreenSock/pen/03a0bf209de49a8cef8fffb1b0eb4eab)

[根据滚动速度施加不同的变形程度](https://codepen.io/GreenSock/pen/eYpGLYL)

[滚动展示图片对比效果](https://codepen.io/GreenSock/pen/oNjgEjm)

[图片懒加载预加载](https://codepen.io/GreenSock/pen/OJMaEOP)

[平滑滚动](https://codepen.io/GreenSock/pen/gOgWELo)

[超流畅轮播图](https://codepen.io/GreenSock/pen/RwKwLWK)

[手机端常用下滑隐藏上滑显示固定头部](https://codepen.io/GreenSock/pen/qBawMGb)

[下滑变小回顶部变高固定头部](https://codepen.io/GreenSock/pen/mdVyPvK)



### Draggable

> 使所有dom都可拖拽，旋转，滚动，与InertiaPlugin完美集成，实现惯性效果。

**完整参数**

```js
Draggable.create("#yourID", {
  type: "rotation", // 拖拽类型。默认"x,y"，基于 translateX 和 translateY 变换。可选值有 "left,top", "rotation", "x", "y", "left", "top"
  bounds: '#bounds', // 边界。Dom | String | Object。让可拖拽元素保持在另一个元素的边界内。也可将边界定义为一个虚空的矩形下，如{top:100，left:0，width:1000，height:800}，它基于父级坐标系。或最大最小值{minX:10，maxX:300，minY:50，maxY:500}，或{minRotation: 0, maxRotation: 270}.
  cursor: 'grab', // 光标。默认情况下值为"move"，除了 type: "rotation"是"default"。
  activeCursor: 'grabbing', // 正在拖拽时的光标。
  allowContextMenu: true, // 允许右键菜单。默认fasle。
  allowEventDefault: true, // 取消事件的默认动作。默认false。
  allowNativeTouchScrolling: false, // 允许默认触摸滚动。默认true。
  autoScroll: 1, // 拖拽元素超出边界时，自动滚动速度。默认值0，不自动滚动。1代表正常速度，2代表2倍速度。
  dragClickables: false, // 默认true。如果想让 a，button，input等可点击并禁止拖拽，设置为false。
  clickableTest: fun, // 默认情况下，可拖拽元素里的点击元素 a，button，只可点击不可拖拽。可以设置 dragClickables: true 来让可点击元素也可以拖拽。clickableTest 传入点击的元素，来精确控制这一属性。
  dragResistance: 0.5, // 拖拽阻力。0~1之间的一个数字。1完全不允许拖动，0.5半速。 
  edgeResistance: 0.5, // 超出边界的阻力。0~1之间的一个数字。1完全不允许超出边界，0.5半速。
  force3D: false, // 强制3D渲染。默认true。如果想禁用3D渲染，设置为false。
  minimumMovement: 5, // 最小拖动判定。默认值2。
  lockAxis: true, // 如果true。则在水平或垂直方向拖动超过2像素，就锁定该坐标轴，只允许在这个坐标轴上拖动。
  liveSnap: true, // Function|Boolean|Array|Object, 定义拖动元素时应用的规则。
                  // 当作为函数。传入一个数字参数然后返回一个数字。比如要捕捉最近的50增量。liveSnap:function（value）{return Math.round（value/50）*50；}
                  // 当作为布尔值。捕捉snap定义的值。
                  // 当作为数组。拖动时将在数组中找到最接近的值吸附。
                  // 当作为对象。可以对x, y 设置不同吸附规则。
  // 事件回调
  callbackScope: object, // 回调作用域。onDrag、onDragEnd、onDragStart等。
  onClick: callback, // 点击回调
  onClickParams: callback, // 点击回调参数
  onDrag: callback, // 拖拽回调
  onDragParams: callback, // 拖拽回调参数
  onDragEnd: callback, // 拖拽结束回调
  onDragEndParams: callback, // 拖拽结束回调参数
  onDragStart: callback, // 拖拽开始回调
  onDragStartParams: callback, // 拖拽开始回调参数
  onLockAxis: callback, // 锁定坐标回调
  onMove: callback, // 移动回调
  onPress: callback, // 按下回调
  onPressParams: callback, // 按下回调参数
  onPressInit: callback, // 按下之前回调
  onRelease: callback, // 释放回调
  onReleaseParams: callback, // 释放回调参数
  // InertiaPlugin 惯性插件相关, Club会员权益
  inertia: true, // Boolean|Object。惯性。
  snap: {x: xFun, y: yFun} , // Function|Object|Array。 定义元素释放后在何处着陆的规则。传入当前值，返回一个处理值。
  onThrowUpdate: callBack, // 释放元素后，到吸附着陆期间每次更新调用的函数
  onThrowComplete: callBack, // 惯性动画完成的回调
  throwResistance: 500, // 惯性阻力。默认1000
  maxDuration: 3, // 惯性动画最大持续时间。默认10秒
  minDuration: 0.5, // 惯性动画最小持续时间。默认0.2秒
  overshootTolerance: 2, // 惯性过冲量。默认1。设置为0，不允许过冲。
  trigger: '.handler', // Element|String|Object, 触发拖动区域。如果希望特定部位触发拖动，而不是整个元素，设置一个子元素作为触发器。
  zIndexBoost: false, // 默认true。默认情况下，点击的元素的层级将设为较高的值，默认1000，如果想禁用此行为，设为false。
});
```

**Demo**

[codepen Draggable How-To Pen](https://codepen.io/collection/AtuHb)

[拟物风数字滚动选择器](https://codepen.io/chucktaylor/pen/vYxLBZx?editors=1010)

[超出滚动](https://codepen.io/GreenSock/pen/YPvdYv/?editors=001)

[拖入逻辑判断](https://codepen.io/GreenSock/pen/GFBvn)



### CSSRule

> 对于像::before ::after这样js无法获取的伪类，此插件可以使用css规则添加动画

```js
var rule = CSSRulePlugin.getRule(".myClass::before"); //get the rule
gsap.to(rule, {duration: 3, cssRule: {color: "blue"}});
```

css变量

```js
gsap.to(document.documentElement, {duration:2, "--my-variable": 100});
```



### MotionPath

> 路径动画。
>
> 支持DOM， SVG，Canvas等



### Pixi

> 使用pixi.js 更容易



### Easel

> 使用EaselJS更容易



### ScrollTo

> 设置窗口滚动位置的动画



### ColorProps

> 颜色色值转换



### Modifiers



### text



## Club插件

**简单版**

[DrawSVG](https://greensock.com/docs/v3/Plugins/DrawSVGPlugin)

[Physics2D](https://greensock.com/docs/v3/Plugins/Physics2DPlugin)

[PhysicsProps](https://greensock.com/docs/v3/Plugins/PhysicsPropsPlugin)

[ScrambleText](https://greensock.com/docs/v3/Plugins/ScrambleTextPlugin)

[CustomBounce](https://greensock.com/docs/v3/Eases/CustomBounce)

[CustomWiggle](https://greensock.com/docs/v3/Eases/CustomWiggle)

**加强版**

> 包含简单版，以及以下插件

[Flip](https://greensock.com/docs/v3/Plugins/Flip) 

[GSDevTools](https://greensock.com/docs/v3/Plugins/GSDevToolsPlugin)

[Inertia](https://greensock.com/docs/v3/Plugins/InertiaPlugin)

[MorphSVG](https://greensock.com/docs/v3/Plugins/MorphSVGPlugin)

[MotionPathHelper](https://greensock.com/docs/v3/Plugins/MotionPathHelper)

[SplitText](https://greensock.com/docs/v3/Plugins/SplitText)

**商业版**

> 包含简单版、加强版，以及以下许可证

[Commercial license](https://greensock.com/licensing)

