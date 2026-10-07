---
date: '2025-07-01 11:04:51'
tags: []
title: STM32 FreeRTOS基础
updated: '2025-12-29 15:04:10'
---
# 一、配置
## 1、CubeMX新工程配置
与普通逻辑不同，下面的时基源(Timebase Source)我选的是TIM6(是一个`基本定时器`)，然后在NVIC将TIM6的`优先级`设置为0
![](img/Pasted%20image%2020250710092715.png)
![](img/Pasted%20image%2020250710092900.png)
