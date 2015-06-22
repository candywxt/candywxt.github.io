---
layout: post
title:  "判断两个数组是否相似"
date:   2015-06-21 14:41:30
categories: jekyll update
---

> 现在才开始步入正轨，写代码的感觉越来越好了。

题目来源：[http://www.imooc.com/code/5760](http://www.imooc.com/code/5760);

> 题目要求：请在index.html文件中，编写arraysSimilar函数，实现判断传入的两个数组是否相似。具体需求：
1. 数组中的成员类型相同，顺序可以不同。例如[1, true] 与 [false, 2]是相似的。
2. 数组的长度一致。
3. 类型的判断范围，需要区分:String, Boolean, Number, undefined, null, 函数，日期, window.
当以上全部满足，则返回"判定结果:通过"，否则返回"判定结果:不通过"。
     
拿到题目的时候答案已经给出了，看了下评论说可以先排序，再通过判断字符串的方法能够更快的实现，我一想确实是这样，于是有了以下代码：
{% highlight javascript linenos%}
     function arraysSimilar(arr1, arr2){
            if(!(arr1 instanceof Array) || !(arr2 instanceof Array)) return false;
            if(arr1.length !== arr2.length) return false;
            var len = arr1.length;
            var temp1 = [];
            var temp2 = [];
            for(var i = 0;i < len;i++){
                var r1 = Object.prototype.toString.apply(arr1[i]);
                var r2 = Object.prototype.toString.apply(arr2[i]);
                temp1.push(r1);
                temp2.push(r2);
            }
            //console.log(a,b)
            return temp1.sort().join() == temp2.sort().join() ? true:false;
        }
{% endhighlight %}
首先判断是不是数组，不是则返回，其次判断数组长度是否一致，不是则返回。然后定义两个临时数组来存放arr1,arr2里成员的类型，因为数组内可能为基础类型和对象,所以用typeof 并不能满足我们的要求，所以用了
Object.prototype.toString方法。将临时数组排序，这样因为数组长度一致，排序之后如果两个数组中数据类型是一致的，则排序之后的结果就是完全相同的，接着拼接成数组，如果相同则判断通过。不同则失败。

关于数据结构请移步MDN[https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Data_structures](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Data_structures)

我秉承学习的原则，要通读老师的代码，先贴出男神Bosn的答案：
{% highlight javascript linenos %}
    /**
     * String, Boolean, Number, undefined, null, 函数，日期, window
     */
    function arraysSimilar(arr1, arr2) {
        // 判断参数，确保arr1, arr2是数组，若不是直接返回false
        if (!(arr1 instanceof Array)
            || !(arr2 instanceof Array)) {
            return false;
        }
        // 判断长度
        if (arr1.length !== arr2.length) return false;
        var i = 0, 
            n = arr1.length, 
            countMap1 = {},  // 用来计算数组元素数据类型个数的map，key是TYPES中的类型字符串，value是数字表示出现次数。
            countMap2 = {},
            t1, t2,
            TYPES = ['string', 'boolean', 'number', 'undefined', 'null',
                'function', 'date', 'window'];
        // 因为是无序的，用一个对象来存储处理过程。key为类型, value为该类型出现的次数。
        // 最后校验：若每一种数据类型出现的次数都相同（或都不存在），则证明同构。
        for (; i < n; i++) {
            t1 = typeOf(arr1[i]);
            t2 = typeOf(arr2[i]);
            if (countMap1[t1]) {
                countMap1[t1]++;
            } else {
                countMap1[t1] = 1;
            }
            if (countMap2[t2]) {
                countMap2[t2]++;
            } else {
                countMap2[t2] = 1;
            }
        }
        // 因为typeof只能判断原始类型，且无法判断null(返回"object")，所以自己写typeof方法扩展。
        function typeOf(ele) {
            var r;
            if (ele === null) r = 'null'; // 判断null
            else if (ele instanceof Array) r = 'array';  // 判断数组对象
            else if (ele === window) r = 'window';  // 判断window
            else if (ele instanceof Date) r = 'date'  // 判断Date对象
            else r = typeof ele; // 其它的，使用typeof判断
            return r;
        }
     
        for (i = 0, n = TYPES.length; i < n; i++) {
            if (countMap1[TYPES[i]] !== countMap2[TYPES[i]]) {
                return false;
            }
        }
     
        return true;
    }
{% endhighlight %}

男神的意思大概是这样的：

1、先确保两个都是数组；

2、确保两个数组长度相等；

3、判断数组成员。且如果数据类型出现的个数相同或都没有出现则为同构。


补充了typeOf函数，其实也就是相当于Object.prototype.toString()，这样typeOf既可以判断null,array,window,date又可以判断原始类型。
定义的TYPES数组和我们的sort的功能是一样的，这样在比较的时候就可以比较相同数据出现的个数，如果个数相同则一致不相同则不一致。
我用sort的话排序也可以实现这样的效果。

就是这样了~

