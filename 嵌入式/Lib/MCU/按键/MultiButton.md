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


# 函数

## 核心API

#### `void button_init(Button* handle, uint8_t(*pin_level)(uint8_t), uint8_t active_level, uint8_t button_id)`

**功能**: Initialize button instance  
**参数**:

- `handle`: 按键句柄
- `pin_level`: GPIO 读取函数指针
- `active_level`: 有效电平 (0 或 1)
- `button_id`: 按键 ID

#### `void button_attach(Button* handle, ButtonEvent event, BtnCallback cb)`

**功能**: Attach event callback function  
**参数**:

- `handle`: 按键句柄
- `event`: 事件类型
- `cb`: 回调函数

#### `void button_detach(Button* handle, ButtonEvent event)`

**功能**: Detach event callback function  
**参数**:

- `handle`: 按键句柄
- `event`: 事件类型

#### `int button_start(Button* handle)`

**功能**: Start button processing  
**返回值**: 0=成功, -1=已存在, -2=参数错误

#### `void button_stop(Button* handle)`


**功能**: Stop button processing

#### `void button_ticks(void)`

**功能**: Background processing function (call every 5ms)

### 工具函数

#### `ButtonEvent button_get_event(Button* handle)`

**功能**: Get current button event

#### `uint8_t button_get_repeat_count(Button* handle)`


**功能**: Get repeat press count

#### `void button_reset(Button* handle)`

**功能**: Reset button state to idle

#### `int button_is_pressed(Button* handle)`

**功能**: Check if button is currently pressed  
**返回值**: 1=按下, 0=未按下, -1=错误

# 用法与示例

## 简单用法（轮询+uwTick无阻塞延时）

步骤：
1. 初始化button
2. 给按键添加事件句柄
3. 启动按键处理
4. 定时执行`button_ticks`函数


我这里用key.h和key.c堆multibutton的一些用法进行封装了一下，让代码整体管理方便一些。

`main.c`
``` c
int main(void)
{
  HAL_Init();

  SystemClock_Config();

  MX_GPIO_Init();

  key_init();
  printf("hello world\n");

  while (1)
  {
	if(uwTick - lastTime >= 5)
	{
		key_loop();
		lastTime = uwTick;
	}

  }
}
```

`key.c`

函数
1. 两个主要的函数：`key_init`和`key_loop`
2. `read_button_gpio`读取gpio状态函数，使用button_id来筛选
3. button事件回调函数，通过`button_attach`传入函数指针，再通过multi_button中间件的状态机驱动，如果发生`attach`过的事件，则调用该函数指针，来执行相应操作。


``` c
#include "key.h"
#include "multi_button.h"

static Button btn1, btn2, btn3, btn4;

// Hardware abstraction layer function
// This simulates reading GPIO states
uint8_t read_button_gpio(uint8_t button_id)
{
    switch (button_id) {
        case 1:
            return HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_13);
        case 2:
            return HAL_GPIO_ReadPin(GPIOD, GPIO_PIN_10);
        case 3:
            return HAL_GPIO_ReadPin(GPIOD, GPIO_PIN_9);
        case 4:
            return HAL_GPIO_ReadPin(GPIOD, GPIO_PIN_8);
        default:
            return 0;
    }
}

// Callback functions for button 1
void btn1_single_click_handler(Button* btn)
{
    (void)btn;  // suppress unused parameter warning
    printf("Button 1: Single Click\n");
}

void btn1_double_click_handler(Button* btn)
{
    (void)btn;  // suppress unused parameter warning
    printf("Button 1: Double Click\n");
}

void btn1_long_press_start_handler(Button* btn)
{
    (void)btn;  // suppress unused parameter warning
    printf("Button 1: Long Press Start\n");
}

void btn1_long_press_hold_handler(Button* btn)
{
    (void)btn;  // suppress unused parameter warning
    printf("Button 1: Long Press Hold...\n");
}

void btn1_press_repeat_handler(Button* btn)
{
    printf("Button 1: Press Repeat (count: %d)\n", button_get_repeat_count(btn));
}

// Callback functions for button 2
void btn2_single_click_handler(Button* btn)
{
    (void)btn;  // suppress unused parameter warning
    printf("Button 2: Single Click\n");
}

void btn2_double_click_handler(Button* btn)
{
    (void)btn;  // suppress unused parameter warning
    printf("Button 2: Double Click\n");
}

void btn2_press_down_handler(Button* btn)
{
    (void)btn;  // suppress unused parameter warning
    printf("Button 2: Press Down\n");
}

void btn2_press_up_handler(Button* btn)
{
    (void)btn;  // suppress unused parameter warning
    printf("Button 2: Press Up\n");
}

// Callback functions for button 1
void btn3_single_click_handler(Button* btn)
{
    (void)btn;  // suppress unused parameter warning
    printf("Button 3: Single Click\n");
}

void btn3_double_click_handler(Button* btn)
{
    (void)btn;  // suppress unused parameter warning
    printf("Button 3: Double Click\n");
}

void btn3_long_press_start_handler(Button* btn)
{
    (void)btn;  // suppress unused parameter warning
    printf("Button 3: Long Press Start\n");
}

void btn3_long_press_hold_handler(Button* btn)
{
    (void)btn;  // suppress unused parameter warning
    printf("Button 3: Long Press Hold...\n");
}

void btn3_press_repeat_handler(Button* btn)
{
    printf("Button 3: Press Repeat (count: %d)\n", button_get_repeat_count(btn));
}

// Callback functions for button 1
void btn4_single_click_handler(Button* btn)
{
    (void)btn;  // suppress unused parameter warning
    printf("Button 4: Single Click\n");
}

void btn4_double_click_handler(Button* btn)
{
    (void)btn;  // suppress unused parameter warning
    printf("Button 4: Double Click\n");
}

void btn4_long_press_start_handler(Button* btn)
{
    (void)btn;  // suppress unused parameter warning
    printf("Button 4: Long Press Start\n");
}

void btn4_long_press_hold_handler(Button* btn)
{
    (void)btn;  // suppress unused parameter warning
    printf("Button 4: Long Press Hold...\n");
}

void btn4_press_repeat_handler(Button* btn)
{
    printf("Button 4: Press Repeat (count: %d)\n", button_get_repeat_count(btn));
}

void key_init()
{
	GPIO_InitTypeDef GPIO_Initure;

    __HAL_RCC_GPIOC_CLK_ENABLE();           //开启GPIOC时钟
    __HAL_RCC_GPIOD_CLK_ENABLE();           //开启GPIOD时钟

    GPIO_Initure.Pin = GPIO_PIN_8 | GPIO_PIN_9 | GPIO_PIN_10 ;	//PD8.9.10
    GPIO_Initure.Mode = GPIO_MODE_INPUT;    //输入
    GPIO_Initure.Pull = GPIO_PULLUP;      //下拉
    GPIO_Initure.Speed = GPIO_SPEED_HIGH;   //高速
    HAL_GPIO_Init(GPIOD, &GPIO_Initure);

    GPIO_Initure.Pin = GPIO_PIN_13;         //PC13
    GPIO_Initure.Mode = GPIO_MODE_INPUT;    //输入
    GPIO_Initure.Pull = GPIO_PULLDOWN;        //上拉
    GPIO_Initure.Speed = GPIO_SPEED_HIGH;   //高速
    HAL_GPIO_Init(GPIOC, &GPIO_Initure);
	
	
    // Initialize button 1 (active high for simulation)
    button_init(&btn1, read_button_gpio, 1, 1);
    
    // Attach event handlers for button 1
    button_attach(&btn1, BTN_SINGLE_CLICK, btn1_single_click_handler);
    button_attach(&btn1, BTN_DOUBLE_CLICK, btn1_double_click_handler);
    button_attach(&btn1, BTN_LONG_PRESS_START, btn1_long_press_start_handler);
    button_attach(&btn1, BTN_LONG_PRESS_HOLD, btn1_long_press_hold_handler);
    button_attach(&btn1, BTN_PRESS_REPEAT, btn1_press_repeat_handler);
    
    // Initialize button 2 (active high for simulation)
    button_init(&btn2, read_button_gpio, 0, 2);
    
    // Attach event handlers for button 2
    button_attach(&btn2, BTN_SINGLE_CLICK, btn2_single_click_handler);
    button_attach(&btn2, BTN_DOUBLE_CLICK, btn2_double_click_handler);
    button_attach(&btn2, BTN_PRESS_DOWN, btn2_press_down_handler);
    button_attach(&btn2, BTN_PRESS_UP, btn2_press_up_handler);
	
    // Initialize button 3 (active high for simulation)
    button_init(&btn3, read_button_gpio, 0, 3);
    
    // Attach event handlers for button 3
    button_attach(&btn3, BTN_SINGLE_CLICK, btn3_single_click_handler);
    button_attach(&btn3, BTN_DOUBLE_CLICK, btn3_double_click_handler);
    button_attach(&btn3, BTN_LONG_PRESS_START, btn3_long_press_start_handler);
    button_attach(&btn3, BTN_LONG_PRESS_HOLD, btn3_long_press_hold_handler);
    button_attach(&btn3, BTN_PRESS_REPEAT, btn3_press_repeat_handler);
	
    // Initialize button 4 (active high for simulation)
    button_init(&btn4, read_button_gpio, 0, 4);
    
    // Attach event handlers for button 4
    button_attach(&btn4, BTN_SINGLE_CLICK, btn4_single_click_handler);
    button_attach(&btn4, BTN_DOUBLE_CLICK, btn4_double_click_handler);
    button_attach(&btn4, BTN_LONG_PRESS_START, btn4_long_press_start_handler);
    button_attach(&btn4, BTN_LONG_PRESS_HOLD, btn4_long_press_hold_handler);
    button_attach(&btn4, BTN_PRESS_REPEAT, btn4_press_repeat_handler);
    
    // Start button processing
    button_start(&btn1);
    button_start(&btn2);
    button_start(&btn3);
    button_start(&btn4);
}	

void key_loop()
{
	button_ticks();
}

```



# 代码解析

## 头文件

## 按键回调函数指针类型定义

> 用于绑定按键状态的回调函数（事件）

``` c
// Button callback function type
typedef void (*BtnCallback)(Button* btn_handle);
```

- 无返回值
- 函数指针名为BtnCallback
- 传入按键结构体指针（pointer to button struct）

### 按键事件类型

``` c
// Button event types
typedef enum {
	BTN_PRESS_DOWN = 0,     // button pressed down
	BTN_PRESS_UP,           // button released
	BTN_PRESS_REPEAT,       // repeated press detected
	BTN_SINGLE_CLICK,       // single click completed
	BTN_DOUBLE_CLICK,       // double click completed
	BTN_LONG_PRESS_START,   // long press started
	BTN_LONG_PRESS_HOLD,    // long press holding
	BTN_EVENT_COUNT,        // total number of events
	BTN_NONE_PRESS          // no event
} ButtonEvent;

```

用enum枚举，代表按键代表的状态，[状态机](#状态机说明)中会用到。

### 按键结构体

``` c
// Forward declaration
typedef struct _Button Button;

// ...

// Button structure
struct _Button {
	uint16_t ticks;                     // tick counter
	uint8_t  repeat : 4;                // repeat counter (0-15)
	uint8_t  event : 4;                 // current event (0-15)
	uint8_t  state : 3;                 // state machine state (0-7)
	uint8_t  debounce_cnt : 3;          // debounce counter (0-7)
	uint8_t  active_level : 1;          // active GPIO level (0 or 1)
	uint8_t  button_level : 1;          // current button level
	uint8_t  button_id;                 // button identifier
	uint8_t  (*hal_button_level)(uint8_t button_id);  // HAL function to read GPIO
	BtnCallback cb[BTN_EVENT_COUNT];    // callback function array
	Button* next;                       // next button in linked list
};
```

## 源文件

