# 正点原子F1战舰V3 8080 TFT-LCD
## CubeMx部分
LCD引脚图
![](img/Pasted%20image%2020250713105025.png)
对应引脚，其实和CubeMX是一致的。
![](img/Pasted%20image%2020250713105928.png)
![](img/Pasted%20image%2020250713105803.png)
![](img/Pasted%20image%2020250713105824.png)
![](img/Pasted%20image%2020250713105834.png)
![](img/Pasted%20image%2020250713105839.png)
![](img/Pasted%20image%2020250713105845.png)
![](img/Pasted%20image%2020250713105852.png)
![](img/Pasted%20image%2020250713113021.png)
在CubeMx的Conectively中的fsmc，通过电路图，Chip Select(片选)也就是CS选择NE4，Memory type选择LCD Interface，LCD Register Select(LCD寄存器选择)也就是RS，选择A10，Data选择16bit的。
![](img/Pasted%20image%2020250713124616.png)
下面的属性，Memory type是LCD Interface，Extended mode选择Enable。
![](img/Pasted%20image%2020250713125943.png)
然后下面参数参考正点原子F1系列的代码
![](img/Pasted%20image%2020250713130050.png)
![](img/Pasted%20image%2020250713130109.png)
最后就是背光了，将PB0设成GPIO_OUTPUT，标签改成LCD_BL即可。
![](img/Pasted%20image%2020250713130232.png)
## 代码部分
将正点原子的LCD示例代码中的LCD库导进去，注意不要往工程（也就是keil工程文件）导入lcd_ex.c文件（也可以先导入，修改后，之后再在keil中移除即可）。
### delay部分
在lcd_ex.c中的最上面写入两个函数，us函数先用HAL_Delay(1)先代替，之后改也不迟。
``` c
void delay_ms(uint32_t nms)
{
	HAL_Delay(nms);
}

void delay_us(uint32_t nus)
{
	HAL_Delay(1);
}
```
### include引用部分
1. 有路径去掉路径，在keil工具中配置。
2. 将usart.h先去掉，代码中的printf也通通先去掉（注释）。
3. 将sys.h改为main.h（或者如果自己有system.h之类的头文件也行）。
4. 数据格式缺失用stdint.h，如果sprintf找不到用stdio.h。
### 背光灯
可以在gpio.c中找到背光部分的引脚定义，位LCD_BL_GPIO_Port，LCD_BL_Pin，这里与正点原子设置的不同，我们需要改正点原子的。
![](img/Pasted%20image%2020250713131112.png)
在lcd.h中，改以下代码（这里需要引入main.h头文件，不然找不到引脚定义了）
![](img/Pasted%20image%2020250713131253.png)
### lcd_init部分修改
在delay_ms(50)之前的代码全部注释，我们cubemx生成的代码代替了这个步骤。
![](img/Pasted%20image%2020250713131501.png)
弄完之后使用一下，发现还是有问题，好像是多重定义的问题。
我们可以看到是HAL_SRAM_Init惹的祸，海岸在