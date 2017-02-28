---
title: '阅读Rythm.js,了解Web Audio API 实现音频可视化'
date: 2017-02-28 15:13:24
comments: true
tags:
  - 技术
  - 鬼懿IT-干货分享
categories: 技术

---

## Rythm.js源文文档

	1. https://github.com/Okazari/Rythm.js

## 思路

1. 是什么，解决什么问题
2. 什么样子，预览
3. 如何使用
4. 如果工作的（源码分析，基本流程）
5. 总结，收获

## What

>一个可以使也页面跳动的js库。

demo：https://okazari.github.io/Rythm.js/

<!--more-->

## How to Use

按照[README.md](https://github.com/Okazari/Rythm.js/blob/master/README.md) 说明（提供了一版[中文版README.zh-CN.md](https://github.com/JobbyM/Rythm.js/blob/master/README.zh-CN.md)），可以分为两种方式。一种是直接在页面通过`<script>`标签引入Rythm.js 文件，另外一种就是ES6 模块的方式进行使用。上述demo 中就是使用的第一种方式。

第一步：在你的页面引入 rythm。

```html
<script type="text/javascript" src="/path/to/rythm.js"></script>
```

其中的`/path/to/rythm.js ` 既可以是本地也可以是线上地址`https://unpkg.com/rythm.js/`

第二步：引入rythm css 类的元素将会跳舞。

```html
<div class="rythm-bass"></div>
```

其中的`rythm-bass` 可以进行自定义，也可以使用Rythm.js 默认的值，稍后将在How to Work 中进行介绍。

=第三步：创建一个Rythm 对象，并且传入你的音频url 地址，然后调用start 方法。这样就可以查看效果了。

```javascript
var rythm = new Rythm();
rythm.setMusic("path/to/sample.mp3");
rythm.start();
```

## How to Work

原理：

使用Web Audio API 实现的音频可视化。通过获取各个时间点上的音频数据（通常为振幅或频率），之后运用图像技术将其处理为视觉输出（例如一个图像）来实现。Web Audio API 提供了一个不会改变输入信号的音频节点--分析器节点（AnalyserNode），通过它获取声音数据并传递到像`<canvas>` 等等一样的可视化工具。

一般流程如下：

1、创建音频环境
2、在音频环境中，创建声源
3、创建特效节点
4、选择音频的终点
5、将声源、特效节点以及音频的终点链接起来

Web Audio API 的最简代码：

```js
// 创建音频环境
var audioCtx = new (window.AudioContext || window.webkitAudioContext)();

// 在音频环境中，创建声源
var oscillator = audioCtx.createOscillator();

// 选择音频的终点
// 将声源、特效节点以及音频的终点链接起来
oscillator.connect(audioCtx.destination);

//
oscillator.start()
```

然后将上述js 代码引入一个HTML 页面就可以了，***注意：需要调整一下电脑的音量***

上述代码只是一个简单使用Web Audio API 的例子，因此同介绍的一般流程不同，缺失了流程中的部分。下面根据一般流程来解释Rythm.js 中的代码。

## Review Code

1、 [创建音频环境](https://github.com/JobbyM/Rythm.js/blob/master/src/rythm.js#L5-L7)

```js
  that._browserAudioCtx = AudioContext || webkitAudioContext

  that._audioCtx = new that._browserAudioCtx();
```

2、[在音频环境中，创建声源](https://github.com/JobbyM/Rythm.js/blob/master/src/rythm.js#L46-L55)

```js
  that._createSourceFromAudioElement = function connectExternalAudioSource(audioElement) {
    return that._audioCtx.createMediaElementSource(that._audio);
  }

  that.connectExternalAudioElement = function connectExternalAudioElement(audioElement) {
    that._audio = audioElement;
    that._rythmInputType = that._rythmInputTypeList['EXTERNAL'];
    that._source = that._createSourceFromAudioElement(that._audio);
    that._connectSource(that._source);
  }
```

以及[获取麦克风中的音频流](https://github.com/JobbyM/Rythm.js/blob/master/src/rythm.js#L73-L95)

```js
  that.plugMicrophone = function plugMicrophone(){
    return that._getMicrophoneStream().then(function(stream){
      that._audio = stream;
      that._rythmInputType = that._rythmInputTypeList['STREAM'];
      that._source = that._audioCtx.createMediaStreamSource(stream);
      that._connectSource(that._source);
    })
  }

  that._getMicrophoneStream = function _getMicrophoneStream(){
    navigator.getUserMedia = (navigator.getUserMedia ||
                              navigator.webkitGetUserMedia ||
                              navigator.mozGetUserMedia ||
                              navigator.msGetUserMedia);
    return new Promise(function(resolve, reject){
      navigator.getUserMedia({audio:true},
        function(medias){
          resolve(medias);
        },function(error){
          reject(error);
        })
    });
  }
```

3、[创建特效节点](https://github.com/JobbyM/Rythm.js/blob/master/src/rythm.js#L9-L18)

```js
  that._init = function(){
    that._analyser = that._audioCtx.createAnalyser();
    that._gain = that._audioCtx.createGain();
    that._source = {};
    that._audio = {};
    that._hzHistory = [];
    that._analyser.fftSize = 2048;
  }

  that._init();
```

注意，这里实现了两个音效节点（AudioNode）一个是分析器节点（ AnalyserNode），一个是音量节点（GainNode），Rythm.js 主要用到的是分析器节点（AnalyserNode）。

4、[选择音频的终点](https://github.com/JobbyM/Rythm.js/blob/master/src/rythm.js#L60-L61)，这里根据音源的不同，选择不同的音频重点，只有当音源不是来自麦克风才使用系统自带的扬声器。

```js
    if(that._rythmInputType !== that._rythmInputTypeList['STREAM']){
      that._analyser.connect(that._audioCtx.destination);
```

5、[将声源、特效节点以及音频的终点链接起来](https://github.com/JobbyM/Rythm.js/blob/master/src/rythm.js#L57-L64)

```js
  that._connectSource = function _connectSource(source){
    source.connect(that._gain);
    that._gain.connect(that._analyser);
    if(that._rythmInputType !== that._rythmInputTypeList['STREAM']){
      that._analyser.connect(that._audioCtx.destination);
      that._audio.addEventListener("ended", that.stop);
    }
  }
```

上述就是Rythm.js 源码中对应一般流程中的相关代码，接下来主要是通过对分析器节点（AnalyserNode）获取的数据进行分析，来实现Web Audio API 的可视化。

## Visualizations with Web Audio API

通过调用[AnalyserNode.getByteFrequencyData()](https://developer.mozilla.org/zh-CN/docs/Web/API/AnalyserNode/getByteFrequencyData) 获取频率数据，并将其存储到Unit8Array（无符号字节数组）中。

```js
 that._analyser.fftSize = 2048;

 that._frequences = new Uint8Array(that._analyser.frequencyBinCount);

 that._analyser.getByteFrequencyData(that._frequences);
```

上述代码相当于:

```js
that._analyser.getByteFrequencyData( new Uint8Array(1024));
```

通过上述操作，我们就获取到了音频数据。

我们定义 [renderRythm()](https://github.com/JobbyM/Rythm.js/blob/master/src/rythm.js#L127-L149) 函数来进行可视化。

```js
  function renderRythm() {
    // 判断是否需要进行渲染
    if(that.stopped){
      return;
    }
    // 获取音频数据，并存到_frequences 数组中
    that._analyser.getByteFrequencyData(that._frequences);
    // 遍历_frequences 数组中的数据
    for(var i=0; i<that._frequences.length; i++){
      // 初始化二维数组
      if(!that._hzHistory[i]){
        that._hzHistory[i] = [];
      }
      // 确保二维数组长度为maxValueHistory 的值
      if(that._hzHistory[i].length > that.maxValueHistory){
        that._hzHistory[i].shift();
      }
      that._hzHistory[i].push(that._frequences[i]);
      // 无效代码
      var value = that._frequences[i];
    }
    // 遍历页面中的所有需要跳动的元素
    that.rythmMapping.forEach(function(mappingItem){
      // 根据type 选择跳动的类型  
      switch (mappingItem.type) {
        default:
          // 应用到元素的CSS scale 属性值上
          pulseSize(mappingItem.elementClass, getAverageRatio(mappingItem.startValue, mappingItem.nbValue));
      }
    })
    // 保持持续更新
    requestAnimationFrame(renderRythm);
  }
```

[getAverageRatio()](https://github.com/JobbyM/Rythm.js/blob/master/src/rythm.js#L151-L157) 获取平均比率。

```js
  function getAverageRatio(startingValue, nbValue){
    var total = 0;
    for(var i=startingValue; i<nbValue+startingValue; i++){
      total += getRatio(i);
    }
    return total/nbValue;
  }
```

[getRatio()](https://github.com/JobbyM/Rythm.js/blob/master/src/rythm.js#L159-L174) 获取当前比率。

```js
  function getRatio(index){
    var min = 255;
    var max = 0;
    // 遍历_hzhHistory[index] 这个二维数组，获取最大值max 和最小值
    for(var i = 0; i < that._hzHistory[index].length; i++){
      if(that._hzHistory[index][i] < min){
        min = that._hzHistory[index][i];
      }
      if(that._hzHistory[index][i] > max){
        max = that._hzHistory[index][i];
      }
    }
    // 返回值的计算方法(
    var scale = max - min;
    var actualValue = that._frequences[index] -min;
    var percentage = (actualValue/scale);
    return that.startingScale + (that.pulseRatio * percentage);
  }
```

[pulseSize()](https://github.com/JobbyM/Rythm.js/blob/master/src/rythm.js#L176-L181)  设置元素的scale 值。

```js
  function pulseSize(name, value){
    var elements = document.getElementsByClassName(name);
    for(var i = 0; i < elements.length; i++){
      elements[i].style.transform = 'scale('+value+')'
    }
  }
```

## 总结

1、对Web Audio API 有了初步了解
2、阅读源代码确实对自己有助于提升
3、参考文档比较多，主要是自己对音乐相关的概念不是很清楚

## 参考文档

[Web Audio API](https://www.w3.org/TR/webaudio/ "Web Audio API")
[Web Audio API - Web API 接口|MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Audio_API "Web Audio API - Web API 接口|MDN")
[AudioContext - Web API 接口|MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioContext "AudioContext - Web API 接口|MDN")
[AnalyserNode - Web API 接口|MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/AnalyserNode   "AnalyserNode - Web API 接口|MDN")
[AnalyserNode.fftSize - Web API 接口|MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/AnalyserNode/fftSize "AnalyserNode.fftSize - Web API 接口|MDN")
[GainNode - Web API 接口|MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/GainNode "GainNode - Web API 接口|MDN")
[AudioContext.createGain() - Web API 接口|MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioContext/createGain "AudioContext.createGain() - Web API 接口|MDN")
[AudioContext.createMediaElementSource() - Web API 接口|MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioContext/createMediaElementSource "AudioContext.createMediaElementSource() - Web API 接口|MDN")
[MediaElementAudioSourceNode - Web API 接口|MDN](https://developer.mozilla.org/en-US/docs/Web/API/MediaElementAudioSourceNode "MediaElementAudioSourceNode - Web API 接口|MDN")
[AudioNode - Web API 接口|MDN](https://developer.mozilla.org/en-US/docs/Web/API/AudioNode "AudioNode - Web API 接口|MDN")
[AudioNode.connect() - Web API 接口|MDN](https://developer.mozilla.org/en-US/docs/Web/API/AudioNode/connect "AudioNode.connect() - Web API 接口|MDN")
[AudioContext.createMediaStreamSource() - Web API 接口|MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioContext/createMediaStreamSource "AudioContext.createMediaStreamSource() - Web API 接口|MDN")
[navigator.getUserMedia() - Web API 接口|MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Navigator/getUserMedia "navigator.getUserMedia() - Web API 接口|MDN")
[GainNode.gain - Web API 接口|MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/GainNode/gain "GainNode.gain - Web API 接口|MDN")
[媒体流（MediaStream）- Web API 接口|MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaStream "媒体流（MediaStream）- Web API 接口|MDN")
[MediaStreamTrack - Web API 接口|MDN](https://developer.mozilla.org/en-US/docs/Web/API/MediaStreamTrack "MediaStreamTrack - Web API 接口|MDN")
[AnalyserNode.getByeFrequencyData - Web API 接口|MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/AnalyserNode/getByteFrequencyData "AnalyserNode.getByeFrequencyData - Web API 接口|MDN")
[网页音频接口基本概念 - Web API 接口|MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Audio_API/Basic_concepts_behind_Web_Audio_API "网页音频接口基本概念 - Web API 接口|MDN")
[[HTML5] Web Audio API 打造超炫的音乐可视化效果](http://www.cnblogs.com/qieguo/p/5405303.html "[HTML5] Web Audio API 打造超炫的音乐可视化效果")
[开大你的音响，感受HTML5 Audio API 带来的视听盛宴](http://www.cnblogs.com/Wayou/p/3543577.html "开大你的音响，感受HTML5 Audio API 带来的视听盛宴")
[如果看了此文你还不懂傅立叶变换，那就过来掐死我吧【完整版】- 文章 - 伯乐在线](http://blog.jobbole.com/70549/ "如果看了此文你还不懂傅立叶变换，那就过来掐死我吧【完整版】- 文章 - 伯乐在线")
