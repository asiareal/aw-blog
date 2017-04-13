---
title: javascript 浮点数计算精度问题
date: 2017-04-13 21:27:51
categories: 笔记
tags:
  - javascript
---
> 今天，工作中遇到浮点数运算精度问题，之前也有印象但是没做系统总结。为了保证今后不犯相同的错误， 现查询资料后总结如下。

## 问题描述

浏览器输入
```javascript
0.1 + 0.2 != 0.3 // =>true
```
返回结果为`true`

chrome 控制台输入`0.1 + 0.2` 结果：
```javascript
0.30000000000000004
```

## 产生原因
这是什么原因呢，我记的大学呢时候老师讲浮点数精度问题，但许久没碰到这种问题也都忘记了，今天专门查
寻了一下资料，一起来探究下面原理

### 计算机存储原理
JS 遵循 IEEE 754 规范，采用双精度存储（double precision），占用 64 bit。如图

![双精度存储](/uploads/double/双精度存储.png)

*说明：*
- 1位用来表示符号位
- 11位用来表示指数
- 52位表示尾数

### 十进制转二进制算法
计算机以以上方法存储，那么计算机的二进制实现和位数限制有些数无法有限表示。就像一些无理数不能有限表示，
如 圆周率 3.1415926...，1.3333... 等。

下面通过介绍十进制转二进制算法来理解这部分，十进制转换为二进制，分为整数部分和小数部分 

#### 整数部分 
方法：除2取余法，即每次将整数部分除以2，余数为该位权上的数，而商继续除以2，余数又为上一个位权上的数，这个步骤一直持续下去，直到商为0为止，最后读数时候，从最后一个余数读起，一直到最前面的一个余数。下面举例： 
例：将十进制的168转换为二进制 

得出结果 将十进制的168转换为二进制，（10101000）2 
分析:第一步，将168除以2,商84,余数为0。 
第二步，将商84除以2，商42余数为0。 
第三步，将商42除以2，商21余数为0。 
第四步，将商21除以2，商10余数为1。 
第五步，将商10除以2，商5余数为0。 
第六步，将商5除以2，商2余数为1。 
第七步，将商2除以2，商1余数为0。 
第八步，将商1除以2，商0余数为1。 
第九步，读数，因为最后一位是经过多次除以2才得到的，因此它是最高位，读数字从最后的余数向前读，即10101000 

#### 小数部分 
方法：乘2取整法，即将小数部分乘以2，然后取整数部分，剩下的小数部分继续乘以2，然后取整数部分，剩下的小数部分又乘以2，一直取到小数部分 
为零为止。 **如果永远不能为零，就同十进制数的四舍五入一样，按照要求保留多少位小数时，就根据后面一位是0还是1，取舍，如果是零，舍掉，如果是1，向前入一位。换句话说就是0舍1入。** 读数要从前面的整数读到后面的整数，下面举例： 
例1：将0.125换算为二进制 

得出结果：将0.125换算为二进制（0.001）2 
分析：第一步，将0.125乘以2，得0.25,则整数部分为0,小数部分为0.25; 
第二步, 将小数部分0.25乘以2,得0.5,则整数部分为0,小数部分为0.5; 
第三步, 将小数部分0.5乘以2,得1.0,则整数部分为1,小数部分为0.0; 
第四步,读数,从第一位读起,读到最后一位,即为0.001。 

理解小数部分计算原理后，那么：
```
0.1 >>转为2进制>> 0.0001 1001 1001 1001…（1001无限循环）
0.2 >>转为2进制>> 0.0011 0011 0011 0011…（0011无限循环）
```
出现这种情况就会 **0舍1入** 也就是会导致精度出现问题的原因。

## 解决方法
对于整数，前端出现问题的几率可能比较低，毕竟很少有业务需要需要用到超大整数，只要运算结果不超过 Math.pow(2, 53) 就不会丢失精度。

对于小数，前端出现问题的几率还是很多的，尤其在一些电商网站涉及到金额等数据。解决方式：把小数放到位整数（乘倍数），再缩小回原来倍数（除倍数）

```javascript
/**
 * floatTool 包含加减乘除四个方法，能确保浮点数运算不丢失精度
 *
 * 我们知道计算机编程语言里浮点数计算会存在精度丢失问题（或称舍入误差），其根本原因是二进制和实现位数限制有些数无法有限表示
 * 以下是十进制小数对应的二进制表示
 *      0.1 >> 0.0001 1001 1001 1001…（1001无限循环）
 *      0.2 >> 0.0011 0011 0011 0011…（0011无限循环）
 * 计算机里每种数据类型的存储是一个有限宽度，比如 JavaScript 使用 64 位存储数字类型，因此超出的会舍去。舍去的部分就是精度丢失的部分。
 *
 * ** method **
 *  add / subtract / multiply /divide
 *
 * ** explame **
 *  0.1 + 0.2 == 0.30000000000000004 （多了 0.00000000000004）
 *  0.2 + 0.4 == 0.6000000000000001  （多了 0.0000000000001）
 *  19.9 * 100 == 1989.9999999999998 （少了 0.0000000000002）
 *
 * floatTool.add(0.1, 0.2) >> 0.3
 * floatTool.multiply(19.9, 100) >> 1990
 *
 */
var floatTool = function() {
    
    /*
     * 判断obj是否为一个整数
     */
    function isInteger(obj) {
        return Math.floor(obj) === obj
    }
    
    /*
     * 将一个浮点数转成整数，返回整数和倍数。如 3.14 >> 314，倍数是 100
     * @param floatNum {number} 小数
     * @return {object}
     *   {times:100, num: 314}
     */
    function toInteger(floatNum) {
        var ret = {times: 1, num: 0}
        if (isInteger(floatNum)) {
            ret.num = floatNum
            return ret
        }
        var strfi  = floatNum + ''
        var dotPos = strfi.indexOf('.')
        var len    = strfi.substr(dotPos+1).length
        var times  = Math.pow(10, len)
        var intNum = parseInt(floatNum * times + 0.5, 10)
        ret.times  = times
        ret.num    = intNum
        return ret
    }
    
    /*
     * 核心方法，实现加减乘除运算，确保不丢失精度
     * 思路：把小数放大为整数（乘），进行算术运算，再缩小为小数（除）
     *
     * @param a {number} 运算数1
     * @param b {number} 运算数2
     * @param digits {number} 精度，保留的小数点数，比如 2, 即保留为两位小数
     * @param op {string} 运算类型，有加减乘除（add/subtract/multiply/divide）
     *
     */
    function operation(a, b, op) {
        var o1 = toInteger(a)
        var o2 = toInteger(b)
        var n1 = o1.num
        var n2 = o2.num
        var t1 = o1.times
        var t2 = o2.times
        var max = t1 > t2 ? t1 : t2
        var result = null
        switch (op) {
            case 'add':
                if (t1 === t2) { // 两个小数位数相同
                    result = n1 + n2
                } else if (t1 > t2) { // o1 小数位 大于 o2
                    result = n1 + n2 * (t1 / t2)
                } else { // o1 小数位 小于 o2
                    result = n1 * (t2 / t1) + n2
                }
                return result / max
            case 'subtract':
                if (t1 === t2) {
                    result = n1 - n2
                } else if (t1 > t2) {
                    result = n1 - n2 * (t1 / t2)
                } else {
                    result = n1 * (t2 / t1) - n2
                }
                return result / max
            case 'multiply':
                result = (n1 * n2) / (t1 * t2)
                return result
            case 'divide':
                return result = function() {
                    var r1 = n1 / n2
                    var r2 = t2 / t1
                    return operation(r1, r2, 'multiply')
                }()
        }
    }
    
    // 加减乘除的四个接口
    function add(a, b) {
        return operation(a, b, 'add')
    }
    function subtract(a, b) {
        return operation(a, b, 'subtract')
    }
    function multiply(a, b) {
        return operation(a, b, 'multiply')
    }
    function divide(a, b) {
        return operation(a, b, 'divide')
    }
    
    // exports
    return {
        add: add,
        subtract: subtract,
        multiply: multiply,
        divide: divide
    }
}();
```

## `toFixed` 不会四舍五入

按照上面存储原理，不同浏览器`toFixed`表现不一，有可能出现不会四舍五入的情况，可以用以下函数代替

```javascript
// toFixed 修复
function toFixed(num, s) {
    var times = Math.pow(10, s)
    var des = num * times + 0.5
    des = parseInt(des, 10) / times
    return des + ''
}
```

## 参考资料

[1] [JavaScript数字精度丢失问题总结](https://www.cnblogs.com/snandy/p/4943138.html)

[2] [二进制、八进制、十进制、十六进制之间转换](http://www.cnblogs.com/lds85930/archive/2007/09/19/897912.html)
