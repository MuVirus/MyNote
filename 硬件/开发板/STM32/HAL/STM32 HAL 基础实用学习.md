# GPIO 
# 一些函数

```c
/* IO operation functions *****************************************************/
GPIO_PinState     HAL_GPIO_ReadPin(GPIO_TypeDef *GPIOx, uint16_t GPIO_Pin);
void              HAL_GPIO_WritePin(GPIO_TypeDef *GPIOx, uint16_t GPIO_Pin, GPIO_PinState PinState);
void              HAL_GPIO_TogglePin(GPIO_TypeDef *GPIOx, uint16_t GPIO_Pin);
HAL_StatusTypeDef HAL_GPIO_LockPin(GPIO_TypeDef *GPIOx, uint16_t GPIO_Pin);
void              HAL_GPIO_EXTI_IRQHandler(uint16_t GPIO_Pin);
void              HAL_GPIO_EXTI_Callback(uint16_t GPIO_Pin);
```

## HAL_GPIO_WritePin

```c
/**
  * @brief  Set or clear the selected data port bit.
  *
  * @note   This function uses GPIOx_BSRR and GPIOx_BRR registers to allow atomic read/modify
  *         accesses. In this way, there is no risk of an IRQ occurring between
  *         the read and the modify access.
  *
  * @param  GPIOx where x can be (A..G) to select the GPIO peripheral for STM32G4xx family
  * @param  GPIO_Pin specifies the port bit to be written.
  *         This parameter can be any combination of GPIO_PIN_x where x can be (0..15).
  * @param  PinState specifies the value to be written to the selected bit.
  *         This parameter can be one of the GPIO_PinState enum values:
  *            @arg GPIO_PIN_RESET: to clear the port pin
  *            @arg GPIO_PIN_SET: to set the port pin
  * @retval None
  */
```

- 第一个参数：GPIOx (x 为 A..G)
- 第二个参数：GPIO_PIN_x（x 为 0..15）
- 第三个参数：GPIO_PIN_RESET / GPIO_PIN_SET （低电平/高电平）

## GPIO速度

低速（Low Speed）：通常为2MHz或更低。
中速（Medium Speed）：通常为10MHz或20MHz。
高速（High Speed）：通常为50MHz。
非常高（Very High Speed）：通常为100MHz或更高（某些高性能STM32系列支持）。

## GPIO 八种模式及工作原理

[https://blog.csdn.net/as480133937/article/details/98063549](https://blog.csdn.net/as480133937/article/details/98063549)

## GPIO 外部中断(EXIT)

[https://blog.csdn.net/as480133937/article/details/98983268](https://blog.csdn.net/as480133937/article/details/98983268)

### 问题 1 抖动消除

# USART 串口通信

## printf 函数重定向

### 宏定义法

需要引用 stdio.h 这个头文件

```
uint8_t USART2_Tx_Buff[200];
#define u1_printf(...) HAL_UART_Transmit(&huart1, USART2_Tx_Buff, sprintf((char *)USART2_Tx_Buff, __VA_ARGS__), 0xffff);
```

1. 其中...和 __VA_ARGS__   在宏定义中代表可变参数，并且不限数量。
2. HAL_UART_Transmit()第三个参数这里直接使用了 sprintf 是因为 sprintf 返回值为字符个数，sprintf 还可以处理字符串的参数写入问题。
3. 0xffff 表示一个比较大的时间延迟，也可以使用 HAL_MAX_DELAY（更大）

### 其他
在usart.h加入stdio.h
在usart.c的最下方加入
```
int fputc(int ch, FILE *f)
{
    HAL_UART_Transmit(&huart1, (uint8_t *)&ch, 1, HAL_MAX_DELAY);
    return ch;
}
```
# 按键 Key

## 消抖

### 阻塞式
```c
uint8_t B1_status;
uint8_t B1_last_status = 1;

void key_scan()
{
	B1_status = HAL_GPIO_ReadPin(GPIO_PIN_A, GPIO_PIN_0);
	if(B1_status) {
		HAL_Delay(10);
		B1_status = HAL_GPIO_ReadPin(GPIO_PIN_A, GPIO_PIN_0);
		if(B1_status) {
			task_fun();
		}
	}
}
```
### 非阻塞式
非阻塞式我一般喜欢直接用系统嘀嗒器(uwTick)
第一种方法是直接外部定义计时变量，第二种方法是定义一个带有计时变量的结构体按钮类型。
# 定时器
## 定时中断

## 计数器

## 信号输出

## 信号输入（读取频率）

## 信号输入（读取占空比）

# 调度器
## 时间轮询
``` c
#include "scheduler.h"

typedef struct {
	void (*task_fun)(void);
	uint32_t rate_time;
	uint32_t last_time;
} task_t;

static task_t tasks[] = {
	{},
};

uint8_t taskNum;

void scheduler_init()
{
	taskNum = sizeof(tasks) / sizeof(tasks[0]);
}

void scheduler_loop()
{	
	for(uint8_t i = 0; i < taskNum; i++)
	{
		uint32_t now_time = uwTick;
		
		if(now_time >= tasks[i].rate_time + tasks[i].last_time)
		{
			tasks[i].last_time = now_time;
			
			tasks[i].task_fun();
		}
	}
}

```