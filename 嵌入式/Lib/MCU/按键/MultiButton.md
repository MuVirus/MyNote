# 简介

> 开源项目：
> https://github.com/0x1abin/MultiButton

项目中我们单片机需要的就只有两个文件.c.h文件（multi_button开头的）。

我们只需要简单应用的话，可以直接参考`basic_example`。

# 步骤

## 快速上手

> 注：
> 在项目作者的基础上进行修改
### 1. 包含头文件

```c
#include "multi_button.h"
```

### 2. 定义按键实例

```c
static Button btn1;
```

### 3. 实现 GPIO 读取函数

```c
uint8_t read_button_gpio(uint8_t button_id)
{
    switch (button_id) {
        case 1:
            return HAL_GPIO_ReadPin(BUTTON1_GPIO_Port, BUTTON1_Pin);
        default:
            return 0;
    }
}
```

### 4. 初始化按键

```c
// 初始化按键 (active_level: 0=低电平有效, 1=高电平有效)
button_init(&btn1, read_button_gpio, 0, 1);
```

### 5. 注册事件回调

```c
void btn1_single_click_handler(void* btn)
{
    printf("Button 1: Single Click\n");
}

button_attach(&btn1, BTN_SINGLE_CLICK, btn1_single_click_handler);
```

### 6. 启动按键处理

```c
button_start(&btn1);
```

### 7. 定时调用处理函数

```c
// 在 5ms 定时器中断中调用
void timer_5ms_interrupt_handler(void)
{
    button_ticks();
}
```

> 注：
> 只是要多费一个定时器，如果主程序几乎不阻塞的话，用uwTick系统滴答器来非阻塞延时5ms试试。

## 配置

在multi_button.h中可配置
``` c
#define TICKS_INTERVAL          5       // 定时器中断间隔 (ms)
#define DEBOUNCE_TIME_MS        15      // 去抖时间 (ms)
#define SHORT_PRESS_TIME_MS     300     // 短按时间阈值 (ms)
#define LONG_PRESS_TIME_MS      1000    // 长按时间阈值 (ms)
#define PRESS_REPEAT_MAX_NUM    15      // 最大重复计数
```

## 状态机说明

```
[IDLE] --按下--> [PRESS] --长按--> [LONG_HOLD]
   ^                |                    |
   |             抬起|                 抬起|
   |                v                    |
   |          [RELEASE] <----------------+
   |          |       ^
   |       超时|       |快速按下
   |          |       |
   +----------+   [REPEAT]
```

> 注：
> 这个状态机流程可以学习一下。

# 用法与示例



