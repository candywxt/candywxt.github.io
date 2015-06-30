---
layout: post
title:  "使用JS编写输入日期开始倒计时"
date:   2015-06-20 19:58:30
categories: jekyll update
---

> 以绝大多数人的努力程度，根本谈不上拼天赋

题目：按要求yyyy-mm-dd输入日期，点击开始倒计时则可显示输入时间和当前时间的差值，并开始倒计时，在时间为0时，结束倒计时。

拿到这个题目之后，首先写dom结构
{% highlight html linesons%}
   <!-- 一个input输入框 -->
   <input type = 'text' id = 'input'>
   <!-- 一个按钮 -->
   <button id = 'btn'>开始</button>
   <!-- 一个div用来显示倒计时时间 -->
   <div id = 'output'>
   </div>
{% endhighlight %}
自上而下的去编写这个程序，首先我们需要在点击开始的时候触发倒计时程序，我们不妨这样写
{% highlight javascript linesons%}
   //我们用上一次封装好的函数来给按钮添加click事件
   $.on('#btn','click',startTimer)
{% endhighlight %}
那么我们开始想如何编写startTimer函数，首先想要倒计时，需要取到input输入框的值，接着就可以倒计时了
{% highlight javascript linesons%}
   function startTimer(){
    var input = $('#input').value;
    if (!input) {
       alert('input time yyyy-mm-dd');
        return;
    }
    runTimer();
   }
{% endhighlight %}
这时我们会发现，并不是所有传进来的input值我们都能用，所有我们需要一个将它转换为可用的日期格式
这时需要考虑dateParse()函数；
{% highlight javascript linesons%}
   function dataParse(source) {
    var reg = new RegExp("^\\d+(\\-|\\/)\\d+(\\-|\\/)\\d+\x24");
    if('string' == typeof source) {
            if (reg.test(source) || isNaN(Date.parse(source))) {
                var d = source.split(/ |T/),
                d1 = d.length > 1
                    ? d[1].split(/[^\d]/)
                    : [0,0,0],
                d0 = d[0].split(/[^\d]/);
                return new Date(d0[0] -0,
                                d0[1] -1,
                                d0[2] -0,
                                d1[0] -0,
                                d1[1] -0,
                                d1[2] -0);
            }else {
                 return new Date(source);
            }
     }
    return new Date();
  }
{% endhighlight %}
所以我们的startTimer函数则需要添加一句话
{% highlight javascript linesons%}
  targetTime = dataParse(input);
{% endhighlight %}
接着有了目标时间之后，就要开始倒计时，编写我们的runTimer函数
{% highlight javascript linesons%}
   function runTimer(first) {
        var nowTime = new Date();
        var leftTime = targetTime - nowTime;
        if (first && leftTime < 0) {
            alert('目标时间小于当前时间');
            return;
        }
        printTime(leftTime);
        if (leftTime / 1000 == 0) {
            return;
        }
        timer = setTimeout(runTimer,1000);
   }
{% endhighlight %}
这个函数的目的是得到现在时间，现在时间和目标时间的差值，然后不断调用自己刷新差值。我们要尽量做到一个函数只做一件事情。
接着来考虑我们的printTime函数：
{% highlight javascript linesons%}
   function printTime(leftTime) {
        var left Date = {
            dd: parseInt(leftTime / 1000 / 60 / 60 / 24,10),
            hh: parseInt(leftTime / 1000 / 60 / 60 % 24,10),
            mm: parseInt(leftTime / 1000 / 60 % 60 ,10),
            ss: parseInt(leftTime / 1000 % 60,10)
        };
        //得到时间之后，我们还需要将时间显示出来，同样我们需要将时间进行格式化
        $('#output').innerHTML = ''
            + dataFormat(targetTime,'距离yyyy年mm月dd日')
            + format('还有#{dd}'天#{hh}小时#{mm}分#{ss}秒',leftDate);
   }
{% endhighlight %}

先看看dataFormat做了些什么吧！
{% highlight javascript linesons%}
   function dataFormat(source,pattern) {
    if('string' != typeof pattern) {
        return source.toString();
    }
    function replacer(patternPart,result) {
        pattern = pattern.replace(patternPart,result);
    }
    var year = source.getFullYear(),
        month = source.getMounth()+1,
        date2 = source.getDate(),
        hours = source.getHours(),
        minutes = source.getMinutes(),
        seconds = source.getSeconds();
        
        replacer(/yyyy/g,pad(year,4));
        
   }
{% endhighlight %}
pad函数是将日期数零补齐；
{% highlight javascript linesons%}
    function pad (source,length) {
        var pre = "",
        negative = (source < 0),
        string = String(Math.abs(source));
    if(string.length < length) {
        pre = (new Array(length - string.length + 1)).join('0');
    }
    return (negative ? "-":"") + pre +string; 
    }
{% endhighlight %}
将leftDate格式化输入到屏幕上
{% highlight javascript linesons%}
    function format(source,opts) {
        source = String(source);
        var data = Array.prototype.slice.call(arguments,1),toString = Object.prototype.toString;
        if(data.length){
            data = data.length == 1?
            (opts !== null && (/\[object Array]|[\object Object\]/.test(toString).call(opts)))? opts:data):data;
        return source.replace(/#\{(.+?)\}/g,function(match,key) {
            var replacer = data[key];
            if('[object Function]' == toString.call(replacer)) {
                replacer = replacer(key);
                }
            return ('undefined' == typeof replacer ? '':replacer);
            });
        }
        return source;
    }
{% endhighlight %}
至此所有的函数已经写完了。感觉自己写的和老师的真的差距好大。首先老师的每个函数都只做了一件事情，根据函数名可以很清楚的知道函数做了什么。
其次，老师对数据进行了三次处理就是为了能够让用户可以比较随意的输入数字，第一：先将输入的字符串转化为时间，再将时间补零显示在屏幕上。第二：我们的时间差得到的是毫秒，现将时间差变成dd,hh,mm,ss这样的对象，
接着讲我们得到的差值对象一一填进我们给他预留的地方#{dd},即format函数要做的事情。这样只要runTimer每秒执行一次就可以实现倒计时效果了。

之前看过一些倒计时的程序，很多都是讲函数冗杂在一起，每个函数都老长老长的，看了ife老师的review才知道原来只要想做什么就先写个函数咯，然后再一步一步慢慢实现…

编程之路还有很长，需要继续努力。