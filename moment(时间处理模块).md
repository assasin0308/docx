# moment(时间处理模块)

[TOC]

[moment中文网](http://momentjs.cn/timezone/)

[日期和相关工作处理类](https://www.leixuesong.cn/1842)



```
'use strict'
let moment = require('moment');
//获取当前时间
var now = moment().toDate();
console.log(now)

//格式化当前时间
now = moment().format('YYYY-MM-DD');
console.log(now);

//其它几种格式化方法
now = moment().format('L') // 10/22/2016 
console.log(now);

now = moment().format('LL') // October 22, 2016
console.log(now);

//格式化当前时间
now = moment().format('YYYY-MM-DD:HH:MM:SS');
console.log(now);

//获取这个月初时间
let startMonth = moment().startOf('month').toDate();
console.log(startMonth);

//获取今天开始的时间
let dayOfStart = moment().startOf('day').toDate();
console.log(dayOfStart);

//获取今天结束的时间
let dayOfEnd = moment().endOf('day').toDate();
console.log(dayOfEnd);


//获取＋n小时
let lateHour = moment().add(2,'hour').toDate();
console.log(lateHour);

//获取＋n小时
console.log('//获取-n小时')
let beforeHour = moment().subtract(2,'hour').toDate();
console.log(beforeHour);


//获取＋n天
let lateDay = moment().add(+5,'day').toDate();
console.log(lateDay);

//获取-n天
let beforeDay = moment().add(-5,'day').toDate();
console.log(beforeDay);
//也可以表示为
beforeDay = moment().subtract(5,'day').toDate();
console.log(beforeDay);

console.log('//获取＋n月')
let lateMonth = moment().add(2,'month').toDate();
console.log(lateHour);

//获取＋n月
let beforeMonth = moment().subtract(2,'month').toDate();
console.log(lateHour);



//获取星期
let week = moment().format('dddd');
console.log(week);

//获取到现在的年限 如果不满一年显示出具体几个月
let years = moment('2020-12-31').fromNow();
console.log(years);
```

