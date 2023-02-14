+++
title =  "风洞实验"
date = "2021-03-05"
weight = 990
categories = [
]
tags = [
  "Nuedc",
]

+++
该实验来源于电赛训练题，核心部分为一个单级PID调节。
<!--more-->

> 该实验来源于电赛训练题，核心部分为一个单级PID调节。

<img src="Wind-Tunnel.JPG" style="zoom:20%;" />

<br/>

## 演示视频
------

{{< bilibili "BV1FU4y1Y7Bj" >}}


## 思维流程
------
![Center](/images/xmind.png#center)

## 实验器材

------


> -	MCU：STM32F103C8T6
> -	 OLED：0.96寸
> -	 MX1508驱动模块
> -	 摩天L1工业激光模块

## 什么是PID？
------
<font size=4>PID控制的实质就是根据输入的偏差值，按照`比例`、`积分`、`微分`的函数关系进行运算，运算结果用以控制输出。</font>

<font size=4>简单来说通过这三个算法的组合可有效地纠正被控制对象的偏差，从而使其达到一个稳定的状态。</font>

> [通俗易懂的PID控制算法讲解](https://www.bilibili.com/video/BV1et4y1i7Gm)
> [PID在线模拟](https://rossning92.github.io/pid-simulation/)

## 位置式PID与增量式PID区别
------
<font size=4>位置式PID算法是一种**非递推**算法，输出结果直接作用于执行机构(PWM)，而增量式PID算法则是一种**递推**算法，输出结果是控制量的**增量。**因为位置式PID中含有积分积分作用相较于**只需要进行输出限幅的增量式PID算法，位置式PID算法还需要进行积分限幅。**</font>

<font size=4>对于一般的控制系统采用两种算法最终效果相差甚小，一般推荐使用只有输出限幅的增量式PID算法，降低调试难易度。</font>


## 代码实现
------
```c
int PID_control(float Encoder,float Target)
{   
    extern float thiserror,lasterror,preerror;
    extern float ep,ei,ed;
    extern float Kp,Ki,Kd;
    thiserror = Target-Encoder;
    ep=thiserror-lasterror;
    ei=thiserror;
    ed=thiserror-2*lasterror+preerror;
    lasterror=thiserror;
    preerror=lasterror;
    return Kp*ep+Ki*ei+Kd*ed;
} 
tem = PID_control(Distance_Data,Distance_Set);
TIM3->CCR1 = TIM3->CCR1+tem<10000 ? TIM3->CCR1+tem>0 ? TIM3->CCR1+tem:0:10000;      ## 10000为此程序PWM波极限值
```

## PID参数整定

<font size=4>PID调整过程一般循序渐进，先将三个参数置为0，先单独调整`比例(Kp)`，调整完成后再调整`积分(Ki)`参数、最后调整`微分(Kd)`参数，初学者可以参考以下文章进行参数的调整。为方便得到具体数据波形，建议采用上位机程序生成波形。这边采用的为`Vofa+`上位机，也可使用`匿名上位机`，最后附上两张PID参数整定后的效果图。</font>
> [PID各个参数含义及整定方法](https://blog.csdn.net/qq1205512384/article/details/72614871)

* PID变换曲线：
![Center](/images/PID_1.gif#center)

* PID稳定曲线：
![Center](/images/PID_2.gif#center)


### 位置式PID算法公式

![Center](/images/PID_3.png#center)

### 增量式PID算法公式


![Center](/images/PID_4.png#center)
<font size=4>风洞实验代码将放在GitHub仓库，需要参考的同学请自行下载。有任何问题可以邮箱留言。</font>