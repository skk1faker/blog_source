---
title: stm32笔记
tags:
- stm32
---

# 基本电路

## 电压与电流

- 电压: 与导体中电子的势能有关,电子从势能高的地方流向势能低的地方,单拿出电压没什么意义,只有两个点间电压不一样才能出现电流, 一定要记住,电压和水压不一样,直观的讲,电压就像滑梯,高处电势高,电压低,电子从高电势流向低电势.

- 电流: 表示单位时间内通过某截面的电子数量,描述的是一个流量, 不是电子速度(光速)

两者之间的关系和电阻相关, 根据欧姆定律可得 : $I * R = V$

- 电阻: 由于电子在导体中流动可能会发生碰撞, 阻止电子通过, 直观的上来看, 电阻越大, 能通过的电子量越少, 于是有 $I = \frac{V}{R}$


# 电路中的元器件

[取自视频](https://www.bilibili.com/video/BV1344y167qm?spm_id_from=333.788.recommend_more_video.-1&vd_source=734d671621ce5093a858cf28daec3a86)

## 二极管

![erjiguan](erjiguan.png)

如上所示，二极管由两类材料组合而成，一类材料易于吸收电子（P(positive)形半导体,在硅晶体中加入了三价硼,容易得到电子）,另一类材料易于施放电子(N(negtive)型半导体, 在硅晶体中添加5价磷,易于失去电子),这样,电子流入P,流出N容易,但是溜出P,流入N困难. 所以形成了单向导电的性质

## pmos和nmos

两种原件可视为一种开关, 与二极管类似,由两类材料组成,形状如下

![cmos](cmos.png)

此时的mos管形成了两个导通方向不同的二极管，所以不能形成电流， mos管可以形成导通沟道来连接两个P区.

在P区加上一个二氧化硅绝缘层和一个金属板形成一个电容结构, 也叫"栅极",让P形成的极板测聚积负电荷,此时两个N区就能连通, 形成了N沟道. 如下图所示.

![n_goudao](n_goudao.png)

在电子元器件中, mos管如下所示, 其中的箭头表示mos管导通时电子的流动方向.

![mos_dianzifangxiang](mos_dianzifangxiang.png)

从这个途中可知,栅极应带正电才能吸引电子,也就是栅极高电平才能将mos管导通.

mos管存在两个特性

1. 栅极电阻很大,故栅极输入不产生电流

因为栅极是一个电容.
![cmos_shanji](cmos_shanji.png) 

2. 栅极容易被静电击穿

mos管一旦击穿就坏了. 此时mos就相当一个二极管.

![mos_jichuan](mos_jichuan.png)

## 什么是运算放大器，有什么作用

![运放](yun_suan_fang_da_qi.png)

运放的一个功能就是利用负反馈做运算,还有一个功能就是

1. 如何计算功放
2. gpio电路原理
3. 功放如何只输出高低电平
4. 施密特触发器

## 自举增压电路

![](zi_ju_zeng_ya.png)

自举增压电路原理是，先将电容充电， 电容充满电后两端会形成电视差，再提高电容电势低的一端电势，另一端电势相应也升高。（相当于两个电池串联，只不过这个电池是通过自己充电产生的）

# stm32相关

## stm32 如何烧录程序

间 

## 如何一键下载

一键下载需要ch341芯片参与，需要能识别到这个芯片

这里给出一键下载的指令
```bash
stm32flash -b 115200 -R -i  '-dtr,rts,dtr,rts'   -w ./build/reboot.bin -v -g 0x08000000 /dev/ttyUSB0
```

我这里写了个脚本

``` bash
#!/bin/bash

# 配置参数（根据实际情况修改）
SERIAL_PORT="/dev/ttyUSB0"  # 串口设备路径（通过 ls /dev/ttyUSB* 查看）
BAUDRATE="115200"           # 波特率（固定，STM32 bootloader 支持 115200）
HEX_FILE=$1 # 程序文件路径（.hex 或 .bin）

# 检查工具是否安装
if ! command -v stm32flash &> /dev/null; then
    echo "错误：未安装 stm32flash，请先安装"
    exit 1
fi

# 检查程序文件是否存在
if [ ! -f "$HEX_FILE" ]; then
    echo "错误：程序文件 $HEX_FILE 不存在"
    exit 1
fi

# 执行烧录（-w 写入，-v 校验，-g 0x0 烧录后从 0x0 地址启动）
# stm32flash -w "$HEX_FILE" -v -g 0x0 -b "$BAUDRATE" "$SERIAL_PORT"
# stm32flash -b 115200 -R -i  '-dtr,rts,dtr,rts'   -w ./build/reboot.bin -v -g 0x08000000 /dev/ttyUSB0
stm32flash -b 115200 -R -i  '-dtr,rts,dtr,rts'   -w ${HEX_FILE} -v -g 0x08000000 ${SERIAL_PORT}
```

不得不说，有时候ai是没用的，硬件问题问ai可能永远都找不到答案




## 输出模式
### 什么是开漏模式,什么是推挽模式


推挽模式就是正常单片机输出0或3.3v的模式

![推挽模式](tui_wan_mo_shi.png)

可以看到INT经过点平反转后只会让PMOS或NMOS出入导通状态，于是OUT输出电平只能是VDD或者“地”

有些场合电器受到电压限制,要求我们不能直接出3.3v,此时我们使用开漏模式


开漏模式需要使PMOS处于开路，配合额外的上拉电阻工作,下面时开漏模式的工作原理图

![开漏模式](kai_lou_mo_shi.png)

此时NMOS导通,压降全部在上拉电阻上，OUT输出低电平，当NMOS关闭，OUT电平与VDD一致




## 输入模式
[参考自文章](https://blog.csdn.net/Seciss/article/details/120595713)  
### 浮空模式、模拟输入

先看一张图

![gpio原理图](fu_kong_shu_ru.png)

浮空输入走得是这条路线
    
![浮空输入路线](fu_kong_route.png)

简单来说就是利用施密特触发器将外接的输入整形放在寄存器内

模拟输入走得路线也比较类似

![模拟输入路线](mo_ni_shu_ru.png)

这种输入不经过施密特触发器,通常用于AD采集io真实电压.

## 复用推挽和开漏

复用推挽开漏。我们知道，gpio的状态和芯片内部寄存器相关，在复用状态下，gpio的状态由外设决定

![gpio原理图](fu_kong_shu_ru.png)
下方复用功能输出就作用于gpio的推挽与开漏


### 上拉下拉模式

### 时钟树

1. 为什么需要时钟

时钟出现为了解决逻辑电路运算需要一定时间的问题，以与门为例，不断变化与门的输入可能会导致与门的输出改变，但是改变需要一段时间，如果任意时刻进行读取，会导致一些时间读到的是错的，所以需要边缘触发器CLK，CLK一段连接输入，一段连接时钟，在时钟上升沿时，输出段电平等于输入端电平，避开运算结果不稳定的时刻。

![CLK,下方输入时钟信号，D端输入，Q端输出,仅在时钟上升沿满足Q电平 = D电平](CLK.png)

时钟树图解



![时钟树全貌](time_clock_tree.png)

2。 什么是HSE,HSI

HSI是芯片内部时钟，依靠rc震荡电路产生时钟，并不精准  
HSE是外部时钟，如果能在板子上看到一个长方形的银色器件，标有频率（8MHZ）多半就是外部时钟了，这个时钟精准，多数使用这个时钟。


一定要注意，使用时时钟树的hse输入频率一定要设置正确。否则可能会出现“usart不能正常使用（波特率设置不准确）”， 定时器定时不精准（hal_delay()定时总是和预想时间不一致）
![hse_config](hse_config.png)


### stm32 系统总线

[参考自](https://blog.csdn.net/qq_62689333/article/details/134367247)

- AHB(Advanced High Performance Bus)，翻译为高级高性能总线。AHB用于高性能模块(如CPU、DMA和DSP等)之间的连接，连接处理器核心、高速存储器和高带宽外设。
    AHB 系统由主模块、从模块和基础结构(Infrastructure)3部分组成，整个AHB总线上的传输都由主模块发出，由从模块负责回应。

- APB(Advanced Peripheral Bus)，翻译为高级外设总线。APB主要用于低带宽的周边外设之间的连接。在STM32中APB又可分为APB1和APB2，APB1、2它们俩都是外设时钟使能寄存器，APB1(RCC_APB1ENR) 、APB2(RCC_APB2ENR) 

具体所连接的外设又有所不同，如下图（STM32F10xxx系统结构图）





![stm32系统总线](system_band.png)

||APB1总线|APB2总线|
|--|:--|:--|
|最大频率|36MHz|72MHz|
|外设|1：DAC接口时钟使能\
2：接口时钟使能\
3：备份接口时钟使能\
4 5：CAN2、1接口时钟使能\
6 7：IIC2、1接口时钟使能\
8-11：USART5、4、3、2接口时钟使能\
12 13：SPI3、2接口时钟使能\
14：窗口看门狗时钟使能(15-20) -7时钟使能|1：USART1接口时钟使能\
2：SPI1接口时钟使能\
3：定时器1时钟使能\
4 5：ADC2、1接口时钟使能\
6-10：端口A-E时钟使能\
11：辅助功能IO时钟使能 |
|可用位|20位，都是R/W，一位对应一个外设，0关闭，1打开|11位，其余的bit保留(保留位读为0)|

## 中断

### NVIC的工作原理

NVIC是ARM Cortex-M系列处理器中的中断控制器，它负责管理和处理所有中断请求。NVIC中有一个中断向量表

，用于存储中断处理程序的入口地址。当一个中断请求发生时，NVIC会根据中断请求的优先级查找中断向量表，找到对应的中断处理程序并执行。

NVIC支持硬件中断和软件中断两种类型。硬件中断是由外部设备触发的中断请求，例如定时器溢出、外部中断、串口接收等；软件中断是由软件程序发起的中断请求，例如操作系统的调度中断、用户自定义的中断等。

NVIC的中断优先级是由两个寄存器控制的：中断优先级寄存器
（IPR）和中断控制和状态寄存器

（ICSR）。IPR寄存器用于设置每个中断的优先级，而ICSR寄存器用于控制中断的使能和处理。

### EXTI外设的工作原理

EXTI（External Interrupt）外设是ARM Cortex-M系列处理器中的外部中断模块，它允许外部设备向处理器发送中断请求。EXTI外设支持多个中断输入线，每个中断输入线都可以配置为上升沿触发、下降沿触发、双边沿触发或者低电平触发。

当一个外部中断请求发生时，EXTI外设会将中断请求发送给NVIC，NVIC根据中断请求的优先级和设置的中断优先级进行处理。处理完成后，NVIC会向EXTI外设发送一个中断清除信号，清除中断请求并允许下一次中断请求。

EXTI外设还支持事件模式和中断模式。事件模式下，当一个中断请求发生时，处理器会执行对应的中断处理程序，但不会向NVIC发送中断清除信号。中断模式下，当一个中断请求发生时，处理器会执行对应的中断处理程序，并向NVIC发送中断清除信号。

三、NVIC和EXTI的应用场景

NVIC和EXTI广泛应用于嵌入式系统和智能硬件中，常见的应用场景包括：

1. 外部中断处理：使用EXTI外设和NVIC中断控制器处理外部设备产生的中断请求，例如按钮按下、传感器检测等。

2. 定时器中断处理：使用NVIC中断控制器处理定时器溢出中断请求，实现定时器功能。

3. 串口通信中断处理：使用NVIC中断控制器处理串口接收中断请求，实现串口通信功能。

4. 操作系统调度中断：使用NVIC中断控制器处理操作系统调度中断请求，实现操作系统的任务调度和管理。
### NVIC中断管理

NVIC（Nested Vectored Interrupt Controller，嵌套向量中断控制器）是ARM Cortex-M系列微控制器中用于管理和控制中断的核心模块。 它的主要作用是接收中断请求，并根据优先级和中断向量表来调度和执行相应的中断服务程序。 简单来说，NVIC负责决定哪个中断优先被响应，以及如何响应中断。
NVIC的核心功能包括：

1. 中断使能和禁用:
NVIC通过寄存器控制哪些中断源是激活的，哪些是禁止的。

2. 中断优先级管理:
NVIC允许设置每个中断源的优先级，确保高优先级中断能够优先得到响应，甚至可以打断正在执行的低优先级中断。
3. 中断向量表管理:
NVIC使用中断向量表来存储每个中断源对应的中断服务程序（ISR）的地址。 当一个中断发生时，NVIC会根据中断向量表找到对应的ISR并执行。
4. 中断挂起和清除:
NVIC可以挂起正在执行的中断，以便处理更高优先级的事件，并在处理完后恢复原来的中断。 它还可以清除已触发的中断请求。
5. 嵌套中断支持:
NVIC支持嵌套中断，即高优先级中断可以打断低优先级中断的执行，提供更灵活的中断响应机制。
6. 低功耗模式支持:
NVIC也参与到低功耗模式管理中，在休眠模式下，它可以响应中断并唤醒系统。

总而言之，NVIC是微控制器中不可或缺的中断管理单元，它确保了系统能够及时、有效地响应各种中断事件，保证系统的稳定性和实时性。

### 简单的说

NVIC的作用就是用来寻找中断函数地址, EXTI用来接收外部信息，然后产生中断给NVIC去查找相关执行函数。

EXTI有20根输入线，用于控制EXTI产生什么类型的中断。

![EXTI电路和相关寄存器的关系](EXTI_elect.png) 

其中中断和事件就是输出的对象不同，中断给NVIC,事件是给外设的。

其中一些输入线连接的就是GPIO的输入，就是这个位置
![EXTI读取输入的位置](EXTI_input.png)

还有一些输入线来源于其他外设，这里给出一张表,表示线连接的外设

|中断/事件线|输入源|
|:--:|:--:|
|EXIT0|PX0（X可为ABCDEFGHI）GPIO输入|
|EXIT1|PX1（X可为ABCDEFGHI）GPIO输入|
|EXIT2|PX2（X可为ABCDEFGHI）GPIO输入|
|EXIT3|PX3（X可为ABCDEFGHI）GPIO输入|
|EXIT4|PX4（X可为ABCDEFGHI）GPIO输入|
|EXIT5|PX5（X可为ABCDEFGHI）GPIO输入|
|EXIT6|PX6（X可为ABCDEFGHI）GPIO输入|
|EXIT7|PX7（X可为ABCDEFGHI）GPIO输入|
|EXIT8|PX8（X可为ABCDEFGHI）GPIO输入|
|EXIT9|PX9（X可为ABCDEFGHI）GPIO输入|
|EXIT10|PX10（X可为ABCDEFGHI）GPIO输入|
|EXIT11|PX11（X可为ABCDEFGHI）GPIO输入|
|EXIT12|PX12（X可为ABCDEFGHI）GPIO输入|
|EXIT13|PX13（X可为ABCDEFGHI）GPIO输入|
|EXIT14|PX14（X可为ABCDEFGHI）GPIO输入|
|EXIT15|PX15（X可为ABCDEFGHI）GPIO输入|
|EXTI16|PVD输入|
|EXTI17|RTC闹钟输入|
|EXIT18|USB唤醒事件|
|EXIT19|以太网唤醒事件|

所有GPIOx和EXTIx的连线情况都类似于下方的方式
![GPIO和EXTI连接情况](EXTI_GPIOInput.png)

不同中断调用的中断处理函数是固定的,需要在处理时自行编写.注意5 ～9和10 ～15两组中断分别共用了其他的回调函数. 共用的处理函数区分中断产生的线路可以使用EXTI_GetITStatus来判断.

![中断处理函数](interupt_handle.png)

注意每次执行完中断后，需要将对应中断清除一下，防止请求挂起寄存器中相应位一直为1, 一般会有CUBEX帮我们生成代码并执行。

下面从代码入手，一步一步执行
```c
void SysInterupt_Init(void){
    // 配置gpioA输入
    GPIO_InitTypeDef gpioInput;
    gpioInput.Mode = GPIO_MODE_INPUT;
    gpioInput.Pin = (GPIO_PIN_0);
    HAL_GPIO_Init(GPIOA, &gpioInput);


    EXTI_HandleTypeDef rspHexti;        // 用于获取输出。
    EXTI_ConfigTypeDef pExtiConfig;
    pExtiConfig.GPIOSel = EXTI_GPIOA;
    pExtiConfig.Line = EXTI_LINE_0;             // 中断线, 与GPIOA的pin0对用
    pExtiConfig.Mode = EXTI_MODE_INTERRUPT;     // 事件和中断输出的对象是不一样的
    pExtiConfig.Trigger = EXTI_TRIGGER_FALLING;  // 下降沿触发
    HAL_EXTI_SetConfigLine(&rspHexti, &pExtiConfig);
    HAL_NVIC_SetPriority(EXTI0_IRQn, 15, 15);
    HAL_NVIC_EnableIRQ(EXTI0_IRQn);
}
void EXTI0_IRQHandler(void){
    if(HAL_GPIO_ReadPin(GPIOA,GPIO_PIN_0) == GPIO_PIN_RESET){
        uint16_t LED_PIN = (GPIO_PIN_0 | GPIO_PIN_1 | GPIO_PIN_5);
        Delay(10);
        HAL_GPIO_TogglePin(GPIOB,LED_PIN);
        while(HAL_GPIO_ReadPin(GPIOA,GPIO_PIN_0) == GPIO_PIN_RESET);
    }
    HAL_GPIO_EXTI_IRQHandler(GPIO_PIN_0);       // 清除相应的请求挂起寄存器
}
```

书上说要开启AFIO时钟，但是我用的是cubex生成的代码，没有找到相应AFIO对应的时钟，也没配置。

## 串口

### 串口发送接受数据中断




### 使用dma收发数据

dma收发数据不需要cpu参与，在dma完成数据传输后会产生数据传输完成中断通知cpu

相关函数如下
```cpp
UART_HandleTypeDef huart1;
DMA_HandleTypeDef hdma_usart1_rx;
DMA_HandleTypeDef hdma_usart1_tx;
HAL_UARTEx_ReceiveToIdle_DMA(huart: &huart1, pData: message, Size: sizeof(message));
__HAL_DMA_DISABLE_IT(&hdma_usart1_rx, DMA_IT_HT); // 关闭过半中断
HAL_UARTEx_RxEventCallback(UART_HandleTypeDef *huart, uint16_t size){   // 接收中断回调

```

#### 发送数据寄存器非空中断

#### 接受数据寄存器非空中断
`void HAL_UARTEx_RxEventCallback(UART_HandleTypeDef *huart, uint16_t Size)`

直接重载这个函数就行。

#### 接收过半中断

如果串口接收到数据长度达到规定的最大size一半时，会产生过半中断。关闭的方法就是在接收后调用        `__HAL_DMA_DISABLE_IT(&hdma_usart1_rx,DMA_IT_HT); //关闭DMA传输过半中断`


## 定时器

定时器大小为16bit,可计数值最大为65535,时钟为72hz时，这个计时器不能达到秒级，于是可以使用两个定时器串联或者预分频器和定时器串联的方法。

![](sys_clock.png)

预分频器和定时器类似，对输入脉冲从0计数到n - 1时输出一个脉冲（即获取n个脉冲后输出一个脉冲）。


自动重装载寄存器作用是，在计数器与自动重装载寄存器值相同时会产生能够一个中断并重置计数器。
![](sys_clock_reload.png)

自动重装在寄存器和预分频器都有一个影子寄存器，开启相关功能会激活如下能力：

定时器工作时用预分频器影子寄存器和自动中装载影子寄存器，在一个定时周期内，这些影子寄存器值不会发生改变，当计时器达到自动重装在影子寄存器的值后，会将预分频寄存器影子寄存器和自动重装在影子定时器更新为预分频器和自动重装载定时器的值。这样做的目的是在修改自动重装定时器和预分频器的定时器时不会影响当前的定时周期。否则没有影子寄存器可能会出现定时器值为100,但是自动重装在定时器由200改为50后，定时器需要从100一直计数到65535后才能到50.

![](shadow_clock.png)


## 一些外设

### 锁相环

如下电路，f1为输入晶振的频率，f2是经过一系列处理的输出频率，反馈给鉴相器，鉴相器对比两个信号，得到两个信号的相位差，经过低通滤波发给压控振荡器，压控振荡器可以根据电压输出一定赫兹的信号，最终可以使得f2 = f1。（不用压控振荡器替代晶振的原因是，压控振荡器需要依赖于稳定的电压才能获得稳定的频率，但是稳定电压并不容易获取）
![ ](suo_xiang_huan0.png)

将负反馈过程中加入一个分频器，那么f2会稳定在分频后和f1相等的水平，于是f2被倍频了。
![ ](suo_xiang_huan1.png)

### mpu6050

用于测量

#### mpu6050设置系统时钟

1. x轴运动如何产生时钟信号。

主要依靠x轴运动时，角速度的变化，角速度变化的频率会被锁相环倍频，作为系统时钟，

2. 外部时钟

当然也可以依赖于外部时钟作为系统时钟

#### mpu6050 开始工作的代码

```cpp
#include "mpu6050.h"

uint8_t MPU_Init(){
	MPU_Write(POWER_MANAGER_1, 1 << 7);	// 复位, 清除所有配置
	// 查看机器是否正常
	uint8_t value;
	int ret = MPU_Read(WHO_AM_I, &value);	// 利用who_am_i 判断是否能正确的访问从机
	if(ret == 0 && value == MPU_ADDR){
		float val = 3.14;
		printf("Successful init mpu6050 %f\r\n", val);
	}else if(ret != 0){
		printf("Fail init mpu6050 %d", ret);
		return ret;
	}else {
		printf("Fail init mpu6050 %d", ret);
		return 1;
	}
	MPU_Write(CONFIG_GYROSCOPE,(3<<3));			// 配置角速度量程
	MPU_Write(CONFIG_ACCELEROMETER,(3<<3));		// 配置加速度量程
	MPU_Write(POWER_MANAGER_1, 4);				// 32khz使用32khz的内部时钟作为系统时钟
	MPU_Set_Sample_Rate(50);					// 设置采样频率
	return 0;

}

extern I2C_HandleTypeDef hi2c1;

uint8_t GyroscopeInit(uint8_t value){
}

uint8_t PowerInit(){	// 电源初始化
	uint8_t pData = (1 << 7);
	HAL_I2C_Mem_Write(&hi2c1,MPU_WRITE_ADDR,POWER_MANAGER_1,I2C_MEMADD_SIZE_8BIT,&pData, 1,HAL_MAX_DELAY);
}
uint8_t MPU_Set_Sample_Rate(uint8_t rate){
	int value = 32768 / rate - 1;
	return MPU_Write(SAMPLE_DEVIDE, value);
}
uint8_t MPU_Read_Length(uint16_t reg ,uint8_t *pData, uint16_t length){
	uint8_t ret = HAL_I2C_Mem_Read(&hi2c1, MPU_READ_ADDR, reg,I2C_MEMADD_SIZE_8BIT, pData, length, HAL_MAX_DELAY);
	return ret;
}
uint8_t MPU_Write_Length(uint16_t reg, uint8_t *pData, uint16_t length){
	uint8_t ret = HAL_I2C_Mem_Write(&hi2c1, MPU_WRITE_ADDR, reg,I2C_MEMADD_SIZE_8BIT,pData,length, HAL_MAX_DELAY);
	return ret;
}

uint8_t MPU_Write(uint16_t reg, uint8_t Data){
	return MPU_Write_Length(reg, &Data,1);
}
uint8_t MPU_Read(uint16_t reg, uint8_t *pData){
	return MPU_Read_Length(reg, pData,1);

}
uint8_t MPU_Get_Angle(AngleValue a){
	
}
uint8_t MPU_Get_Templature(float *temp){
	uint8_t data[2];
	int ret = MPU_Read_Length(TEMPERAUTRE_DATA, data,2);
	if(ret != 0){
		return ret;
	}
	int T = (int16_t)((data[0]) << 8) + data[1];
	*temp = (float) T / 340.0f + 36.53f;
	return 0;
}

```

```c
#pragma once
asm(".global _printf_float");


#include <stdio.h>
#include "i2c.h"
#include "stm32f1xx_hal.h"

#define WHO_AM_I 117
#define POWER_MANAGER_1 107 
#define POWER_MANAGER_2 108
#define CONFIG_GYROSCOPE 27
#define CONFIG_ACCELEROMETER 28
#define SAMPLE_DEVIDE 25
#define MOTION_THRESHOLD 31

#define ACCELEROMETER_DATA 59
#define TEMPERAUTRE_DATA 65
#define GYROSCOPE_DATA 67
#define MPU_ADDR 0X68
#define MPU_READ_ADDR (MPU_ADDR << 1)
#define MPU_WRITE_ADDR ((MPU_ADDR << 1) + 1)

typedef struct Angle{
	double x,y,z;
}AngleValue;

uint8_t MPU_Init();
uint8_t GyroscopeInit(uint8_t value);
uint8_t PowerInit();	// 电源初始化
uint8_t MPU_Read_Length(uint16_t reg ,uint8_t *pData, uint16_t length);
uint8_t MPU_Write_Length(uint16_t reg, uint8_t *pData, uint16_t length);
uint8_t MPU_Write(uint16_t reg, uint8_t Data);
uint8_t MPU_Read(uint16_t reg ,uint8_t *pData);
uint8_t MPU_Set_Sample_Rate(uint8_t rate);
uint8_t MPU_Get_Angle(AngleValue a);
uint8_t MPU_Get_Templature(float *temp);

```

所有的设置寄存器的方法可以从文档中找到方法,文档在如下[github库中可以找到](https://github.com/Embedfire-sensor/ebf_sensor_mpu6050_data.git)

#### 注意其中支持浮点数的输出使用如下
头文件中添加`asm(".global _printf_float");`


## stm32 如何调试

调试程序需要使用daq调试器链接开发板，使用的命令如下：

1. 连接上开发板
``` bash
openocd -f /usr/share/openocd/scripts/interface/cmsis-dap.cfg -c "transport select swd" -f /usr/share/openocd/scripts/target/stm32f1x.cfg

```
输出如下
``` bash
skt1faker@nwpu ~/website/notebook/source/_drafts
master* $ openocd -f /usr/share/openocd/scripts/interface/cmsis-dap.cfg -c "transport select swd" -f /usr/share/openocd/scripts/target/stm32f1x.cfg
Open On-Chip Debugger 0.10.0
Licensed under GNU GPL v2
For bug reports, read
        http://openocd.org/doc/doxygen/bugs.html
		swd
		adapter speed: 1000 kHz
		adapter_nsrst_delay: 100
		none separate
		cortex_m reset_config sysresetreq
		Info : CMSIS-DAP: SWD  Supported
		Info : CMSIS-DAP: JTAG Supported
		Info : CMSIS-DAP: Interface Initialised (SWD)
		Info : CMSIS-DAP: FW Version = 1.0
		Info : SWCLK/TCK = 1 SWDIO/TMS = 1 TDI = 0 TDO = 1 nTRST = 0 nRESET = 1
		Info : CMSIS-DAP: Interface ready
		Info : clock speed 1000 kHz
		Info : SWD DPIDR 0x1ba01477
		Info : stm32f1x.cpu: hardware has 6 breakpoints, 4 watchpoints

```

2. 此时使用`gdb-multiarch xxx.elf` 进入带有debug信息的可执行文件中，先打断点或者其他操作。然后`target extended-remote localhost:3333`, 就算即将能对开发板进行调试了, 接着`monitor reset init`、`continue` 即可在原先设置的断电处停下。

```bash
$ gdb blink.elf
> target remote localhost:3333
> monitor reset init
> continue
```
结果如下:
```bash
skt1faker@nwpu ~/my_procedure/LER_REG 
$ gdb-multiarch stm32f103_project.elf                                [22:26:08]                   
GNU gdb (Ubuntu 9.2-0ubuntu1~20.04.2) 9.2  
(gdb) b main()
Function "main()" not defined.
Make breakpoint pending on future shared library load? (y or [n]) Quit
(gdb) b main
Breakpoint 1 at 0x800037e: file main.c, line 22.
(gdb) target remote localhost:3333
Remote debugging using localhost:3333
0x00000000 in ?? ()
(gdb) monitor reset init
target halted due to debug-request, current mode: Thread 
xPSR: 0x01000000 pc: 0x08000414 msp: 0x20010000
(gdb) continue
Continuing.
Note: automatically using hardware breakpoints for read-only addresses.

Breakpoint 1, main () at main.c:22
22              RCC_APB2ENR |= (1<<3);
(gdb) l

```

## 烧录程序时关闭了SWD功能，导致后续无法烧录程序，应该怎么处理

某次烧录后，我再也没法烧录程序了
``` bash
$ burn ./build/LED_DEBUG.elf                                                    [23:23:04]
Open On-Chip Debugger 0.12.0
Licensed under GNU GPL v2
For bug reports, read
	http://openocd.org/doc/doxygen/bugs.html
swd
Info : CMSIS-DAP: SWD supported
Info : CMSIS-DAP: JTAG supported
Info : CMSIS-DAP: FW Version = 1.0
Info : CMSIS-DAP: Interface Initialised (SWD)
Info : SWCLK/TCK = 1 SWDIO/TMS = 1 TDI = 0 TDO = 1 nTRST = 0 nRESET = 1
Info : CMSIS-DAP: Interface ready
Info : clock speed 1000 kHz
Error: Error connecting DP: cannot read IDR：
```

原因是，上一次烧录的程序没有打开这个东西
![swd debug](debug_swd.png)

此时烧录进去就会将pw14、pw13关闭。解决方法为：

按住复位键，在终端输入
```bash
cd /usr/share/openocd/scripts
openocd -f interface/cmsis-dap.cfg -f target/stm32f1x.cfg -c "init; reset halt; flash erase_sector 0 0 last; exit"
```

之后就嫩烧录了，原因未知

## 烧录时，出现双Double fault(双重故障)错误应怎样处理

```bash
Open On-Chip Debugger 0.12.0
Licensed under GNU GPL v2
For bug reports, read
        http://openocd.org/doc/doxygen/bugs.html
        swd
        Info : CMSIS-DAP: SWD supported
        Info : CMSIS-DAP: JTAG supported
        Info : CMSIS-DAP: FW Version = 1.0
        Info : CMSIS-DAP: Interface Initialised (SWD)
        Info : SWCLK/TCK = 1 SWDIO/TMS = 1 TDI = 1 TDO = 1 nTRST = 0 nRESET = 1
        Info : CMSIS-DAP: Interface ready
        Info : clock speed 2000 kHz
        Info : SWD DPIDR 0x2ba01477
        Info : [stm32f4x.cpu] Cortex-M4 r0p1 processor detected
        Info : [stm32f4x.cpu] target has 6 breakpoints, 4 watchpoints
        Info : starting gdb server for stm32f4x.cpu on 3333
        Info : Listening on port 3333 for gdb connections
        [stm32f4x.cpu] halted due to debug-request, current mode: Thread
        xPSR: 0x01000000 pc: 0x08000cd4 msp: 0x20020000
        Info : device id = 0x101f6413
        Info : flash size = 1024 KiB
        auto erase enabled
        wrote 16384 bytes from file build/focf405.elf in 0.842014s (19.002 KiB/s)

        Error: [stm32f4x.cpu] clearing lockup after double fault
        [stm32f4x.cpu] halted due to debug-request, current mode: Handler HardFault
        xPSR: 0x00000003 pc: 0xf3af4804 msp: 0x4c05b4f0
        shutdown command invoked
```

此时的系统出现了严重的错误，（有可能此时芯片进入lockup状态，只有长按reset键才能解除lockup状态）之后重新烧入正常的程序即可。

## 烧录时遇到 “Error: timed out while waiting for target halted”

这中情况非常特殊，这里只给出一种情况的原因
以下为烧录时遇到的情况。
```bash
Open On-Chip Debugger 0.12.0
Licensed under GNU GPL v2
For bug reports, read
        http://openocd.org/doc/doxygen/bugs.html
swd
Info : CMSIS-DAP: SWD supported
Info : CMSIS-DAP: JTAG supported
Info : CMSIS-DAP: FW Version = 1.0
Info : CMSIS-DAP: Interface Initialised (SWD)
Info : SWCLK/TCK = 1 SWDIO/TMS = 1 TDI = 0 TDO = 1 nTRST = 0 nRESET = 1
Info : CMSIS-DAP: Interface ready
Info : clock speed 2000 kHz
Info : SWD DPIDR 0x2ba01477
Info : [stm32f4x.cpu] Cortex-M4 r0p1 processor detected
Info : [stm32f4x.cpu] target has 6 breakpoints, 4 watchpoints
Info : starting gdb server for stm32f4x.cpu on 3333
Info : Listening on port 3333 for gdb connections
Info : [stm32f4x.cpu] external reset detected               // 这句可以看到reset一直被激活，所以不能烧录
Error: timed out while waiting for target halted
TARGET: stm32f4x.cpu - Not halted

```
解决的方法很简单，直接将3.3v电压接到板子的reset引脚上就行, 然后就突然能运行了。





## 串口输入输出使用方法
### minicom使用方法
主要是工具如何配置
![](serier_config.png)
![](serious_output.png) 
主要是这几个选项要配置好，其中Hardware flow control改为no,可以键盘输如, serial device 选择串口设备, 不知道是那个usb就拔插一下连接串口的usb线
#### 串口输入

无需配置，代码写好自动产生。

#### 串口输出

配置未经改变时，直接按键盘会向设备输入数据，但是不会在电脑屏幕上回显，需要将如下配置打开
![](sfhjadf.png)
如上，Ctrl+A Z 后按E即可将输出回显显示出来

[参考](https://blog.csdn.net/Mculover666/article/details/87647810)

### STM32CUBEX上的配置

首先看电路，串口线接上了PA9、PA10,同时PA9 --> USART1_TX, PA10 --> USART1_RX, TX与RX一个用来发送数据，一个用来接受数据，注意，两个串口的解法是Tx接RX,Rx接Tx这样才能正确接受和发送数据。

![串口PA9、PA10被接上了](serior_PA910.png)

了解后，我们在stm32CUBEX上做如下配置
![](config_chunk.png)

首先在(1)处配置，(2),(3)会自动生成，此时生成代码即可开始发送和接受数据.

```cpp
UART_HandleTypeDef huart1;
static void MX_USART1_UART_Init(void)
{

    /* USER CODE BEGIN USART1_Init 0 */

    /* USER CODE END USART1_Init 0 */

    /* USER CODE BEGIN USART1_Init 1 */

    /* USER CODE END USART1_Init 1 */
    huart1.Instance = USART1;
    huart1.Init.BaudRate = 115200;
    huart1.Init.WordLength = UART_WORDLENGTH_8B;
    huart1.Init.StopBits = UART_STOPBITS_1;
    huart1.Init.Parity = UART_PARITY_NONE;
    huart1.Init.Mode = UART_MODE_TX_RX;
    huart1.Init.HwFlowCtl = UART_HWCONTROL_NONE;
    huart1.Init.OverSampling = UART_OVERSAMPLING_16;
    if (HAL_UART_Init(&huart1) != HAL_OK)
    {
        Error_Handler();
    }
    /* USER CODE BEGIN USART1_Init 2 */

    /* USER CODE END USART1_Init 2 */

}
int main(void){
    MX_USART1_UART_Init();
    HAL_UART_Receive(&huart1, (uint8_t *)message, 2, HAL_MAX_DELAY);
    HAL_UART_Transmit(&huart1, (uint8_t *)message, sizeof(message), 100);

}
```

## 配置printf输出到串口

```cpp

int _write(int fd, char *ptr, int len) {
	HAL_UART_Transmit(&huart1, (uint8_t *)ptr, len, HAL_MAX_DELAY);
	//HAL_UART_Transmit_IT(&huart1, (uint8_t *)ptr, len);
	return len;
}

```

## spi 通讯

spi通讯使用4根线
1. 片选线cs，用于选择传输信号的对象
2. 时钟线sclk
3. 主-->从数据传输线
4. 从-->主数据传输线

工作方式如下：
片选线信号为低电平时，设备开始通讯，片选线有多个，可以控制多个设备，但时钟和数据线都是复用的。

![spi_line](spi_line.png)

其中sclk用于同步时钟, mosi表述`master output slave input`, miso表示`master input slave output`, 从机上sdo表示从机`output`, sdi表示从机`input`

控制时钟信号和片选线的设备为spi中的主设备，被控制的就是从设备

其内部数据如下：

![spi_internal](spi_internal.png)

利用锁存器将,根据时钟信号的上升沿/下降沿将移位寄存器里面的数据以维持一位一位的移动.经过8次交换就会将移位寄存器中的数据全部交换。移动后将接收到的数据放在接收缓存器中, 中断或者其它程序就会处理这个数据,然后再讲发送缓存器中的数据放在移位寄存器中.

[spi通信参考](https://www.bilibili.com/video/BV1anHZecEiP?spm_id_from=333.788.videopod.sections&vd_source=734d671621ce5093a858cf28daec3a86)

### ssd1315屏幕的使用

由于屏幕不需要输出数据，所以屏幕的sdo引脚是不存在的。屏幕存在如下引脚：

1. GND: 接地
2. VCC: 电源
3. SCL: 时钟
4. SDA: 传输数据, 连接主机MOSI
5. RES: 当res为低电平时会触发复位。就是恢复出场设置
6. DC: 区分传输的数据是命令/数据的线
7. CS: 片选

## pwm 输出

计数模式对比

![pwd_count_mode](pwd_count_mode.png)

|模式|Center Aligned Mode 1|Center Aligned Mode 2|Center Aligned Mode 3|
|:---:|:---:|:---:|:---:|
|计数方向|从0向上计数到ARR，再从ARR向下计数到0|同左|同左|
|比较事件触发点|仅在向下计数过程中，当CNT=CCR时置位比较标志|同左|(未知)|
|中断/DMA请求|经测试，更新中断会产生两次|||
|应用|foc控制中，需要在pwm周期后半段进行电流采样或者算法更新|需要向上计数时间同步的操作，或者在周期开始后立即触发|需要高频同步响应的应用，如编码器接口、实时更新PWM参数|

| 对比项 | PWM模式1 | PWM模式2 |
| :--- | :--- | :--- |
| **核心逻辑** | `CNT < CCR` 时输出有效电平 | `CNT > CCR` 时输出有效电平 |
| **有效电平条件** | 计数值小于比较值时有效 | 计数值大于比较值时有效 |
| **电平输出关系** | 模式1和模式2的输出电平关系是互补的 | 模式1和模式2的输出电平关系是互补的 |
| **寄存器配置位** | `TIMx_CCMRx`寄存器中的`OCxM`位设置为`110` | `TIMx_CCMRx`寄存器中的`OCxM`位设置为`111` |
| **典型应用场景** | 常规PWM输出，如控制LED亮度、电机速度等 | 生成与模式1互补的PWM信号，常用于互补输出或特定逻辑控制 |

![pwm的config](pwm_alarm_config.png)

# 电机

## 逆变桥的三极管上为什么会并联一个二极管

![](ni_bian_qiao.png)

这个二极管作用是保护三极管关闭时不会被大电流击穿。因为逆变桥需要作用在电机上，电机中的线圈可以看作为一个电感，当三极管导通对应的线圈通电，三极管关闭对应的线圈断电，线圈可看组一个电感，断电发生磁场变化导致线圈产生大电流，如果不存在二极管，则电流会流向未导通的三极管，导致其烧毁，故加上二极管，保证感生电流通过。

**由于有二极管的存在，所以逆变桥的正负极绝对不能接反**。 

[参考自](https://zhuanlan.zhihu.com/p/270264867)

## FOC无刷电机控制

# STM32相关文档和库

## hal库和标准库

hal库是现在stm32公司主要推的一种，封装较为严重，易于移植，标准库现在不维护，只是之前有使用。

两种库的查找链接

- [hal库链接](https://www.st.com/en/embedded-software/stm32cube-mcu-mpu-packages/products.html)
- [hal库文档链接](https://www.st.com/en/embedded-software/stm32cubef1.html#documentation)  
- [标准库链接](https://www.st.com.cn/zh/embedded-software/stm32-standard-peripheral-libraries/products.html)



