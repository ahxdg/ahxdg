---
title: webRTC录音功能
date: 2018-03-30 14:04:35
tags:
    - html
---

> 近期公司老项目中有个录音功能由于chrome浏览器的更新，停止录音时特别慢，由此引出了相关问题

问题原因：
项目所使用的录音插件为：microm.min.js，我搜了一下，在github上有相关的项目：https://zzarcon.github.io/microm/
这个插件在浏览器更新后，对于录音停止后处理数据并返回信息的速度明显慢了很多，特别影响体验。至于为何在更新了浏览器会出现这样的问题，我目前并未发现什么原因。
但有一点是这个插件值得优化的地方，就是把录音的Blob转成MP3数据行为是在停止录音操作发生后执行的。如果改成边录边转，那就不会存在这种问题了..

处理问题的过程：
首先，我找到了格式话了压缩的代码，通过日志找到耗时的代码，就在下面的for里，就是把blob转成mp3的过程，但我想不通的是为什么新版的chrome（64，65）会很慢…

```js
{
  key: "encodeSamplesToMp3",
  value: function(e, t) {
    t = o(l, t);
    //这里的for循环特别耗时
    for (var r, n, a, i, s, f, _ = e[0], u = e[1], c = t.maxSamples, h = _.length, d = new this.lame.Mp3Encoder(t.channels, t.sampleRate, t.kbps), p = [], m = 0; h >= c;)
    m += c,
    n = _.splice(m, m + c),
    a = u ? u.splice(m, m + c) : null,
    r = d.encodeBuffer(n, a),
    r.length > 0 && p.push(new Int8Array(r)),
    h -= c;
    i = d.flush(),
    i.length > 0 && p.push(new Int8Array(i)),
    s = new Blob(p, {
      type: "audio/mp3"
    }),
    f = URL.createObjectURL(s),
    this.mp3Resolver({
      buffer: p,
      blob: s,
      url: f
    })
  }
```
我去查了chrome浏览器的更新文档，但并没有找到相关的信息（可能是我的英文阅读能力太差了8_8）。

在我一筹莫展的时候，我们的运维小哥nick跟我说，我们项目的V2.0上面的录音是ok的！（顿时觉得他的身躯有光芒在照耀着我…）
我打开2.0测试了一下，果然很快。
果断找到项目文件，里面引入了三个相关文件：
lame.min.js
recordmp3.js
worker-realtime.js

而v2.0的做法是在录音时弹出一个新的窗口，录音在新的窗口中处理，完成后把数据传给老窗口。（不清楚为什么会这样，可能是跟下面引发出的问题相关吧。）

其中，lame.min.js是底层处理录音的文件，其他两个文件是对于lame的封装：
代码如下：
recordmp3.js
```js
(function (exports) {
    var MP3Recorder = function (config) {
        var recorder = this;
        config = config || {};
        config.sampleRate = config.sampleRate || 44100;
        config.bitRate = config.bitRate || 128;
        navigator.getUserMedia = navigator.getUserMedia ||
                                 navigator.webkitGetUserMedia ||
                                 navigator.mozGetUserMedia ||
                                 navigator.msGetUserMedia;
        if (navigator.getUserMedia) {
            navigator.getUserMedia({
                audio: true
            },
            function (stream) {
                var context = new AudioContext(),
                    microphone = context.createMediaStreamSource(stream),
                    processor = context.createScriptProcessor(16384, 1, 1),//bufferSize大小，输入channel数，输出channel数
                    mp3ReceiveSuccess, currentErrorCallback;
                config.sampleRate = context.sampleRate;
                processor.onaudioprocess = function (event) {
                    //边录音边转换
                    var array = event.inputBuffer.getChannelData(0);
                    realTimeWorker.postMessage({ cmd: 'encode', buf: array });
                };
                var realTimeWorker = new Worker('/audio/worker-realtime.js');
                realTimeWorker.onmessage = function (e) {
                    switch (e.data.cmd) {
                        case 'init':
                            log('初始化成功');
                            if (config.funOk) {
                                config.funOk();
                            }
                            break;
                        case 'end':
                            log('MP3大小：', e.data.buf.length);
                            if (mp3ReceiveSuccess) {
                                mp3ReceiveSuccess(new Blob(e.data.buf, { type: 'audio/mp3' }));
                            }
                            break;
                        case 'error':
                            log('错误信息：' + e.data.error);
                            if (currentErrorCallback) {
                                currentErrorCallback(e.data.error);
                            }
                            break;
                        default:
                            log('未知信息：', e.data);
                    }
                };
                recorder.getMp3Blob = function (onSuccess, onError) {
                    currentErrorCallback = onError;
                    mp3ReceiveSuccess = onSuccess;
                    realTimeWorker.postMessage({ cmd: 'finish' });
                };
                recorder.start = function () {
                    if (processor && microphone) {
                        microphone.connect(processor);
                        processor.connect(context.destination);
                        log('开始录音');
                    }
                }
                recorder.stop = function () {
                    if (processor && microphone) {
                        microphone.disconnect(processor);
                        processor.disconnect(context.destination);
                        log('录音结束');
                    }
                }
                realTimeWorker.postMessage({
                    cmd: 'init',
                    config: {
                        sampleRate: config.sampleRate,
                        bitRate: config.bitRate
                    }
                });
            },
            function (error) {
                var msg;
                switch (error.code || error.name) {
                    case 'PERMISSION_DENIED':
                    case 'PermissionDeniedError':
                        msg = '用户拒绝访问麦客风';
                        break;
                    case 'NOT_SUPPORTED_ERROR':
                    case 'NotSupportedError':
                        msg = '浏览器不支持麦客风';
                        break;
                    case 'MANDATORY_UNSATISFIED_ERROR':
                    case 'MandatoryUnsatisfiedError':
                        msg = '找不到麦客风设备';
                        break;
                    default:
                        msg = '无法打开麦克风，异常信息:' + (error.code || error.name);
                        break;
                }
                if (config.funCancel) {
                    config.funCancel(msg);
                }
            });
        } else {
            if (config.funCancel) {
                config.funCancel('当前浏览器不支持录音功能');
            }
        }
        function log(str) {
            if (config.debug) {
                console.log(str);
            }
        }
    }
    exports.MP3Recorder = MP3Recorder;
})(window);
```
worker-realtime.js
```js
(function () {
    'use strict';
    importScripts('lame.min.js');
    var mp3Encoder, maxSamples = 1152, samplesMono, lame, config, dataBuffer;
    var clearBuffer = function () {
        dataBuffer = [];
    };
    var appendToBuffer = function (mp3Buf) {
        dataBuffer.push(new Int8Array(mp3Buf));
    };
    var init = function (prefConfig) {
        config = prefConfig || {};
        lame = new lamejs();
        mp3Encoder = new lame.Mp3Encoder(1, config.sampleRate || 44100, config.bitRate || 128);
        clearBuffer();
        self.postMessage({
            cmd: 'init'
        });
    };
    var floatTo16BitPCM = function floatTo16BitPCM(input, output) {
        for (var i = 0; i < input.length; i++) {
            var s = Math.max(-1, Math.min(1, input[i]));
            output[i] = (s < 0 ? s * 0x8000 : s * 0x7FFF);
        }
    };
    var convertBuffer = function (arrayBuffer) {
        var data = new Float32Array(arrayBuffer);
        var out = new Int16Array(arrayBuffer.length);
        floatTo16BitPCM(data, out)
        return out;
    };
    var encode = function (arrayBuffer) {
        samplesMono = convertBuffer(arrayBuffer);
        var remaining = samplesMono.length;
        for (var i = 0; remaining >= 0; i += maxSamples) {
            var left = samplesMono.subarray(i, i + maxSamples);
            var mp3buf = mp3Encoder.encodeBuffer(left);
            appendToBuffer(mp3buf);
            remaining -= maxSamples;
        }
    };
    var finish = function () {
        appendToBuffer(mp3Encoder.flush());
        self.postMessage({
            cmd: 'end',
            buf: dataBuffer
        });
        clearBuffer();
    };
    self.onmessage = function (e) {
        switch (e.data.cmd) {
            case 'init':
                init(e.data.config);
                break;
            case 'encode':
                encode(e.data.buf);
                break;
            case 'finish':
                finish();
                break;
        }
    };
})();
```
赶紧，我写了一个demo，还好可以在localhost下做测试（安全限制：只能在安全的域下使用相关功能）。测试结果：录音停止很快，不在有上述问题。速度上线！

但，第二天问题又来了，一个老师反馈说，chrome标签页上面一直有一个小红点（显示：此标签页正在使用您的摄像头和麦克风，果然歪果仁对于自己的隐私还是很敏感的）

拉出代码来，发现打开页面时直接新建了MP3Recorder对象，更改代码把new MP3Recorder放到了用户触发录音开始的事件里，轻松搞定！

可在我正准备提交代码时，问题又来了，我发现停止录音后，“小红点”还是在的。Ca，这是什么鬼..

赶紧百度，一堆结果里，只有一条相关的。里面说到了localMediaStream.getTracks()[0].stop();
这是什么东西，换google，搜mediastream，肯定跟这玩意儿有关。
原来是通过getUserMedia获取LocalMediaStream对象，然后把其中的track给停掉。
然而，LocaMediaStream已经被instead，可以使用mediaStream，我就在自己写的stop方法中加了这些：
```js
navigator.getUserMedia =  navigator.getUserMedia ||
                          navigator.webkitGetUserMedia ||
                          navigator.mozGetUserMedia ||
                          navigator.msGetUserMedia;
if (navigator.getUserMedia) {
    navigator.getUserMedia({
        audio: true
    },
    function (stream) {
      tracks = stream.getAudioTracks()
      tracks.map(track => {
        stream.removeTrack(track) // 这里是发现stream有个原型方法removeTrack，然而并没有什么用
        track.stop()
      })
    },
    function (err) {
      console.log(err)
    })
}
```
然而还是不行，最后，我又看了一遍recordmp3.js，发现里面也有getUserMedia方法，只能试一试了，我把track.stop()放到了recorder.stop函数里：
```js
recorder.stop = function () {
    if (processor && microphone) {
        microphone.disconnect(processor);
        processor.disconnect(context.destination);
        stream.getTracks()[0].stop()    // 这里
        log('录音结束');
    }
}
```
“小红点”没了，666~

就这样，“小红点”终于消失了，但地址栏还是有个录像机的图标一直显示在那里，暂时不去管他了。有空好好研究一下…