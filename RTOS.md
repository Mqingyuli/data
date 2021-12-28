# 目录

[toc]

## 头文件

```c
//RTOS头文件         #include "FreeRTOS.h"
//任务函数头文件      #include "task.h"
//队列头文件         #include "queue.h"
//信号量互斥量头文件  #include"semphr.h"
```

## 使用建议

```c
/*
1.每个任务函数中最好不要耦合性太强
2.每个任务函数可以通过信号量、消息队列、事件标志组来通信，可以降低耦合度，减少出错率
*/
```

## 1.任务函数

```c
/*
 FreeRTOS 的任务可认为是一系列独立任务的集合。每个任务在自己的环境中运行。在任何时刻，只有一个任务得到运行，FreeRTOS 调度器决定运行哪个任务。调度器会不断的启动、停止每一个任务，宏观看上去所有的任务都在同时在执行。作为任务，不需要对调度器的活动有所了解，在任务切入切出时保存上下文环境（寄存器值、堆栈内容）是调度器主要的职责。为了实现这点，每个 FreeRTOS 任务都需要有自己的栈空间。
 FreeRTOS 中的任务是抢占式调度机制，高优先级的任务可打断低优先级任务，低优先级任务必须在高优先级任务阻塞或结束后才能得到调度。同时 FreeRTOS 也支持时间片轮转调度方式，只不过时间片的调度是不允许抢占任务的 CPU 使用权。
任务通常会运行在一个死循环中，也不会退出，如果一个任务不再需要，可以调用FreeRTOS 中的任务删除 API 函数接口显式地将其删除。
*/
```

![1639198574(1)](E:\Records\资料图片\1639198574(1).jpg)

```c
/*
任务状态迁移
FreeRTOS 系统中的每一个任务都有多种运行状态

(1)：创建任务→就绪态（Ready）：任务创建完成后进入就绪态，表明任务已准备就绪，随时可以运行，只等待调度器进行调度。

(2)：就绪态→运行态（Running）：发生任务切换时，就绪列表中最高优先级的任务被执行，从而进入运行态。

(3)：运行态→就绪态：有更高优先级任务创建或者恢复后，会发生任务调度，此刻就绪列表中最高优先级任务变为运行态，那么原先运行的任务由运行态变为就绪态，依然在就绪列表中，等待最高优先级的任务运行完毕继续运行原来的任务（此处可以看做是 CPU 使用权被更高优先级的任务抢占了）。

(4)：运行态→阻塞态（Blocked）：正在运行的任务发生阻塞（挂起、延时、读信号量等待）时，该任务会从就绪列表中删除，任务状态由运行态变成阻塞态，然后发生任务切换，运行就绪列表中当前最高优先级任务。

(5)：阻塞态→就绪态：阻塞的任务被恢复后（任务恢复、延时时间超时、读信号量超时或读到信号量等），此时被恢复的任务会被加入就绪列表，从而由阻塞态变成就绪态；如果此时被恢复任务的优先级高于正在运行任务的优先级，则会发生任务切换，将该任务将再次转换任务状态，由就绪态变成运行态。

(6) (7) (8)：就绪态、阻塞态、运行态→挂起态（Suspended）：任务可以通过调用 vTaskSuspend() API 函数都可以将处于任何状态的任务挂起，被挂起的任务得不到CPU 的使用权，也不会参与调度，除非它从挂起态中解除。

(9)：挂起态→就绪态：把 一 个 挂 起 状态 的 任 务 恢复的 唯 一 途 径 就 是调 用 vTaskResume() 或 vTaskResumeFromISR() API 函数，如果此时被恢复任务的优先级高于正在运行任务的优先级，则会发生任务切换，将该任务将再次转换任务状态，由就绪态变成运行态。
*/
```

### 1.1声明任务句柄

```c
/*头文件*/
#include "task.h"
TaskHandle_t app_task1_handle=NULL;//要声明任务句柄
```

### 1.2创建任务

```c
//任务原型
//参数一：任务函数名
//参数二：任务的名字测试用 同任务函数名一样即可
//参数三：分配给任务函数的堆栈大小
//参数四：传递给任务函数的参数 没有可为NULL
//参数五：任务优先级0~configMAX_PRIORITIES-1 优先级越小优先级越高
//参数六：任务句柄 
BaseType_t xTaskCreate(TaskFunction_t pxTaskCode,
                       const char * const pcName,
                       const uint16_t usStackDepth,
                       void * const pvParameters,
                       UBaseType_t uxPriority,
                       TaskHandle_t * const pxCreatedTask) 
/* 创建任务1 
app_task1，任务函数
"app_task1"，任务的名字
512，分配给任务1的内存（栈）大小，单位为字
7,优先级为7，数值不能超过（configMAX_PRIORITIES -1）
app_task1_handle任务句柄
*/
xReturn=xTaskCreate(app_task1,"app_task1",512,&app_task1_arg,7,&app_task1_handle);
/*返回值
1. pdTRUE 
表明任务创建成功。
2. errCOULD_NOT_ALLOCATE_REQUIRED_MEMORY 
由于内存堆空间不足，FreeRTOS 无法分配足够的空间来保存任务
*/
```

### 1.3创建任务函数

```c
//任务函数
void app_task1(void *pvParameters)//void类型 参数为void *
{
	//必须死循环 while(1)或for(;;)
	while(1)
	{
		/* 延时1000ms */
		vTaskDelay(1000);//使用RTOS延时释放cpu权限让别的任务调用
		printf("[app_task1] running ...\r\n");
	}
}
```

### 1.4开启任务调度

```c
/* 开启任务调度 */
vTaskStartScheduler();
```
### 1.5删除任务

```c
//删除任务  例如初始化任务函数
 vTaskDelete(Start_Task_Handle);
```

## 2.挂起恢复

```c
/*
vTaskSuspend()
任务可以通过调用 vTaskSuspend()函数都可以将处于任何状态的任务挂起，被挂起的任务得不到 CPU 的使用权，也不会参与调度，它相对于调度器而言是不可见的，除非它从挂起态中解除。

vTaskSuspendAll()
将所有的任务都挂起，就是挂起任务调度器。调度器被挂起后则不能进行上下文切换，但是中断还是使能的。 当调度器被挂起的时候，如果有中断需要进行上下文切换， 那么这个中断将会被挂起，在调度器恢复之后才响应这个中断。

vTaskResume()
任务恢复就是让挂起的任务重新进入就绪状态，恢复的任务会保留挂起前的状态信息，在恢复的时候根据挂起时的状态继续运行。

xTaskResumeFromISR(）
xTaskResumeFromISR()与 vTaskResume()一样都是用于恢复被挂起的任务，不一样的是 xTaskResumeFromISR() 专门用在中断服务程序中。无论通过调用一次或多次vTaskSuspend()函数而被挂起的任务，也只需调用一次xTaskResumeFromISR()函数即可解挂。要想使用该函数必须在FreeRTOSConfig.h中把INCLUDE_vTaskSuspend和INCLUDE_vTaskResumeFromISR 都定义为 1 才有效。任务还没有处于挂起态的时候，调用xTaskResumeFromISR()函数是没有任何意义的
*/
```

###  2.1挂起任务

```c
/*********** 挂起任务，任务2为挂起态**********/
vTaskSuspend(app_task2_handle);
```

### 2.2恢复任务

```c
/* **********恢复任务 ，任务2为就绪态***********/
vTaskResume(app_task2_handle);	
```

## 3.临界区

```c
/*
一旦这部分代码开始执行，则不允许任何中断打扰。为确保临界段代码的执行，在进入临界段之前要关中断，而临界段代码执行完以后要立即开中断。
*/
```

### 3.1进入临界区

```c
/*任务和抢占优先级较低的硬件中断无法打断其执行*/
taskENTER_CRITICAL();
```

### 3.2退出临界区

```c
/* 退出临界区，告诉内核已经保护完毕 */
taskEXIT_CRITICAL();
```

*****

## 4.消息队列

```c
/*
消息队列传递的是实际数据，并不是数据地址，RTX，uCOS-II 和 uCOS-III 是传递的地址）放入到队列。
同样，一个或者多个任务可以通过 RTOS 内核服务从队列中得到消息。通常，先进入消息队列的消息先传
给任务，也就是说，任务先得到的是最先进入到消息队列的消息，即先进先出的原则（FIFO），FreeRTOS
的消息队列支持 FIFO 和 LIFO 两种数据存取方式。
使用消息队列可以让 RTOS 内核有效地管理任务，而全局数组是无法做到的，任务的超时等机制需要用户自己去实现。
使用了全局数组就要防止多任务的访问冲突，而使用消息队列则处理好了这个问题，用户无需担心。
使用消息队列可以有效地解决中断服务程序与任务之间消息传递的问题。
*/
```

### 4.1申请初始化消息队列

```C
/*头文件*/
#include "queue.h"
// 定义队列句柄变量
QueueHandle_t xQueue;
// 申请队列
// 参数 1 : 队列深度
// 参数 2 ： 队列项内容大小
xQueue = xQueueCreate( 10, sizeof( unsigned long ) );
```

### 4.2消息队列发送数据

```c
/****************普通任务函数发送消息***********************/
/*创建要发送的消息类型，类型需与申请的一致*/
unsigned long pxMessage;
/*普通发送消息*/
// 发送消息
// 参数 1 : 队列句柄
// 参数 2 ： 队列内容指针
// 参数 3 ： 允许阻塞时间
//返回值：如果发送到队列成功，则为 pdPASS，否则为 errQUEUE_FULL。
xQueueSend( xQueue,(void *)&pxMessage, ( TickType_t ) 0 );
/****************中断发送消息***********************/	
/*此函数是用于中断服务程序中调用
第 1 个参数是消息队列句柄。
第 2 个参数要传递数据地址，每次发送都是将消息队列创建函数 xQueueCreate 所指定的单个消息大小复制到消息队列空间中。
第3个参数用于保存是否有高优先级任务准备就绪。如果函数执行完毕后，此参数的数值是pdTRUE，说明有高优先级任务要执行，否则没有。
pdTRUE：恢复运行的任务的优先级 >= 正在运行的任务（被中断打断的任务），这意味着在退出中断服务函数后，必须进行一次上下文切换。
pdFALSE：恢复运行的任务的优先级 < 当前正在运行的任务（被中断打断的任务），这意味着在退出中断服务函数后，不需要进行上下文切换。
返回值，如果发送到队列成功，则为 pdPASS，否则为 errQUEUE_FULL。
*/
BaseType_t xQueueSendFromISR
    (
    QueueHandle_t xQueue, /* 消息队列句柄 */
    const void *pvItemToQueue, /* 要传递数据地址 */
    BaseType_t *pxHigherPriorityTaskWoken /* 高优先级任务是否被唤醒的状态保存 */
	);
//例如：
/*发送数据
参数一：个参数是消息队列句柄
参数二：存储数据的地址
参数三：声明的变量  参数三可为NULL
返回值：pdPASS 发送成功 pdTRUE和pdPASS均是发送成功宏定义
*/
//例子一:
//声明变量 高优先级任务是否被唤醒的状态保存  如果不需要可不定义设置为NULL
BaseType_t xHigherPriorityTaskWoken = pdFALSE;
if(pdPASS = xQueueSendFromISR(xQueue1,(void *)&g_uiCount,&xHigherPriorityTaskWoken));
/* 如果xHigherPriorityTaskWoken = pdTRUE，那么退出中断后切到当前最高优先级任务执行 */
portYIELD_FROM_ISR(xHigherPriorityTaskWoken);   
//例子二：
if(pdPASS = xQueueSendFromISR(xQueue1,(void *)&g_uiCount,NULL));
```

### 4.3消息队列接受消息

```c
/****************普通任务函数接收消息***********************/
/*创建要接受的消息类型，类型需与申请的一致*/
unsigned long pxMessage;
// 接收消息
// 参数 1 : 队列句柄
// 参数 2 ： 队列内容返回保存指针
// 参数 3 ： 允许阻塞时间  portMAX_DELAY一直等待
//pdTRUE表示接收到了消息
if( pdTRUE == xQueueReceive( xQueue, &( pxMessage ), ( TickType_t ) 10 ) )
/****************中断接收消息***********************/
/*此函数是用于中断服务程序中调用
第 1 个参数是消息队列句柄。
第 2 个参数指向缓冲区的指针，接收的项目将复制到该缓冲区中
第 3 个参数用于保存是否有高优先级任务准备就绪。如果函数执行完毕后，此参数的数值是pdTRUE，说明有高优先级任务要执行，否则没有。
返回值，如果发送到队列成功，则为 pdPASS，否则为 errQUEUE_FULL。
*/    
BaseType_t xQueueReceiveFromISR
    (
    QueueHandle_t xQueue，
    无效 *pvBuffer,
    BaseType_t *pxHigherPriorityTaskWoken
	);
//例子
//声明变量 高优先级任务是否被唤醒的状态保存  如果不需要可不定义设置为NULL
BaseType_t xHigherPriorityTaskWoken = pdFALSE;
/*发送数据
参数一：个参数是消息队列句柄
参数二：存储数据的地址
参数三：声明的变量 参数三可为NULL
返回值：pdPASS  发送成功 pdTRUE和pdPASS均是发送成功宏定义
*/
//例子一：
if(pdPASS == xQueueReceiveFromISR(xQueue,&buf, &xHigherPriorityTaskWoken ));
//例子二：
if(pdPASS == xQueueReceiveFromISR(xQueue,&buf, NULL ));
```

### 4.4查询消息个数

```c
/*
uxQueueMessagesWaiting
用于查询队列中当前有效数据单元个数。切记不要在中断服务例程中调用 uxQueueMessagesWaiting()。应当在中断服务中
使用其中断安全版本 uxQueueMessagesWaitingFromISR()。
*/
//零表示队列为空
UBaseType_t uxQueueMessagesWaiting( const QueueHandle_t xQueue )
UBaseType_t uxQueueMessagesWaitingFromISR( const QueueHandle_t xQueue )    
```



###  4.5注意事项

```c
/*
1.中断发送读取必须在中断函数中 优先级参数可设置为NULL
2.中断只需要设置一次尽量不要放在任务函数中
3.任务函数的接收无论是接受中断发送的还是普通发送都只能使用普通接收函数
*/
```

*****

## 5.信号量

### 5.1创建信号量

#### 5.1.1创建二值型信号量

```c
//声明二值信号量句柄
SemaphoreHandle_t xSemaphore;
/* 创建二值型信号量 旧版创建*/
// 返回值NULL: 二值信号量创建失败。
vSemaphoreCreateBinary(xSemaphore);

/* 创建二值型信号量 新版创建*/
// 返回值NULL: 二值信号量创建失败。
xSemaphore = xSemaphoreCreateBinary();
```

#### 5.1.2创建计数信号量

```c
/*函数原型
返回值：NULL表示创建失败
参数一：信号量可以计数的最大值
参数二：计数初始值
*/
xSemaphoreCreateCounting( UBaseType_t uxMaxCount,
                                            UBaseType_t uxInitialCount);
//例如：
//声明计数信号量句柄
xSemaphoreHandle xSemaphore;
// 信号量可以计数的最大值为10,计数初始值为0.
xSemaphore = xSemaphoreCreateCounting( 10, 0 );
if( xSemaphore != NULL )
{
    // 信号量创建成功
    // 现在可以使用信号量了。
}
```

#### 5.1.3创建互斥量

```c
//函数原型  返回值为NULL表示创建失败 互斥量不可在中断服务函数中使用
SemaphoreHandle_t xSemaphoreCreateMutex( void );
//例如：
//声明互斥信号量句柄
SemaphoreHandle_t xSemaphore;
/* 创建一个互斥类型的信号量。*/
xSemaphore = xSemaphoreCreateMutex();

if( xSemaphore != NULL )
{
    /* 信号量创建成功并且
       可以使用。*/
}
```

### 5.2释放信号量

```c
/****普通任务释放信号量****/
/*
返回值: 成功返回：pdPASS 失败返回：err_QUEUE_FULL
参数：要释放的信号量句柄
不可在中断服务中使用该函数
*/
xSemaphoreGive(xSemaphore );
/*******中断释放信号量*******/
/*
返回值：成功返回：pdPASS 失败返回：err_QUEUE_FULL
参数一：要释放的信号量句柄
参数二：如果*pxHigherPriorityTaskWoken为pdTRUE，则需要在中断退出前人为的经行一次上下文切换。从FreeRTOS V7.3.0开始，该参数为可选参数，并可以设置为NULL
*/
xSemaphoreGiveFromISR( SemaphoreHandle_t xSemaphore,BaseType_t *pxHigherPriorityTaskWoken );
```

### 5.3获取信号量

```c
/*****普通获取信号量******/
/*
返回值：失败返回：pdFALSE 成功返回：pdPASS
参数一：要获取的信号量句柄
参数二：阻塞时间，信号量无效时等待时间  
为portMAX_DELAY则一直阻塞无限等待；设置0表示不设置等待时间
*/
 xSemaphoreTake( SemaphoreHandle_t xSemaphore, TickType_t xTicksToWait );
/****中断获取信号量****/
/*
返回值：失败返回：pdFALSE 成功返回：pdPASS
参数一：要获取的信号量
参数二：用户只需要提供一 个变量来保存这个值就行了；7.3.0可设置为NULL
*/
xSemaphoreTakeFromISR( SemaphoreHandle_t xSemaphore,signed BaseType_t *pxHigherPriorityTaskWoken );
```

### 5.4对其他信号量的操作

```c
/* 查询信号量的数目 */
UBaseType_t uxSemaphoreGetCount( SemaphoreHandle_t xSemaphore );
/* 查询拥有互斥锁的任务句柄 */
TaskHandle_t xSemaphoreGetMutexHolder( SemaphoreHandle_t xMutex );
```

### 5.5注意事项

```c
/*
1.互斥信号量必须由同一个任务申请，同一个任务释放，其他任务释放无效
2.二值型信号量，一个任务申请成功后可以由另一个任务释放
3.互斥信号量必须归还
*/
```

*****

## 6.软件定时器

### 6.1初始化软件定时器

```c
//创建定时器句柄
TimerHandle_t timer_handle;
//函数原型
//参数一：定时器名称方便识别不同的定时器
//参数二：定时多长时间
//参数三：选择周期模式还是单次模式，若参数为pdTRUE，则表示选择周期模式，若参数为pdFALSE，则表示选择单次模式。
//参数四：定时器id，可选NULL
//参数五：定时器结束调用的回调函数
TimerHandle_t xTimerCreate(	
	const char * const pcTimerName,	 //定时器名称（调试用）
	const TickType_t xTimerPeriodInTicks,//周期（单位tick）
	const UBaseType_t uxAutoReload,//自动装载（pdTURE）
	void * const pvTimerID,//定时器ID，可以判断是哪一个定时器
	TimerCallbackFunction_t pxCallbackFunction //回调函数
)
//示例
timer_handle = xTimerCreate("timer_handle", ms2ticks(5000), pdFALSE, NULL, timer_handle_timeout);
```

### 6.2回调函数

```c
//定时器回调函数
//参数固定
void timer_handle_timeout(TimerHandle_t xTimer);
//示例
void timer_handle_timeout(TimerHandle_t xTimer)
{
    //要执行的代码
}
```

### 6.3软件定时器启动

```c
//成功pdPASS，队列已满pdFAIL
//参数二：为portMAX_DELAY则一直阻塞无限等待；设置0表示不设置等待时间 
BaseType_t xTimerStart(
	TimerHandle_t xTimer,//定时器句柄
	TickType_t xToclsToWait//阻塞等待时间（内部是消息队列）
)
//示例
xTimerStart(timer_handle,0);
//中断中启动软件定时器
BaseType_t xTimerStartFromISR( TimerHandle_t xTimer, BaseType_t *pxHigherPriorityTaskWoken )
//示例
//成功pdPASS，队列已满pdFAIL
BaseType_t xHigherPriorityTaskWoken = pdFALSE;
xTimerStartFromISR( xBacklightTimer, &xHigherPriorityTaskWoken )
```

### 6.4软件定时器停止

```c
//成功pdPASS，队列已满pdFAIL
//停止计时器让其进入休眠
BaseType_t xTimerStop( TimerHandle_t xTimer, 
                      TickType_t xBlockTime )
//示例
xTimerStop(xTimer, 0);
//在中断中停止计时器
BaseType_t xTimerStopFromISR( TimerHandle_t xTimer, BaseType_t *pxHigherPriorityTaskWoken )  
//示例
BaseType_t xHigherPriorityTaskWoken = pdFALSE;
xTimerStopFromISR(xTimer,&xHigherPriorityTaskWoken)
```

### 6.5重启软件定时器

```c
//重启软件定时器
//如果定时器已经启动，重新计算时间
//如果没有启动，则启动
//参数二：为portMAX_DELAY则一直阻塞无限等待；设置0表示不设置等待时间 
BaseType_t xTimerReset(
	TimerHandle_t xTimer,//定时器句柄
	TickType_t xToclsToWait//阻塞等待时间（内部是消息队列）
)
//示例：
xTimerReset(timer_handle,0);
```

### 6.6获取定时器ID

```C
//获取定时器ID pvTimerGetTimerID()
void* pvTimerGetTimerID(TimerHandle_t xTimer);
//示例
pvTimerGetTimerID(timer_handle);
```

### 6.7更改定时器周期

```c
//如果定时器没启动，会启动定时器
//参数二：定时多长时间
//参数三：为portMAX_DELAY则一直阻塞无限等待；设置0表示不设置等待时间 
BaseType_t xTimerChangePeriod(
	TimerHandle_t xTimer,//定时器句柄
	TickType_t xMew{eropd,//新的定时周期
	TickType_t xToclsToWait//阻塞等待时间
)
//示例
xTimerChangePeriod)(timer_handle, 1000, 0);
```
## 7.任务通知
### 7.1发送通知
```c
//参数一：任务句柄
//参数二：忽略
//参数三：忽略
BaseType_t xTaskNotify( TaskHandle_txTaskToNotify,  
                           uint32_t ulValue,  
                           eNotifyAction eAction); 
//宏替换函数：
xTaskNotifyGive(TaskHandle_txTaskToNotify);  
//示例：
/*向prvTask2(),发送通知，使其解除阻塞状态 */  
 xTaskNotifyGive( xTask2 );  
```
### 7.2接收通知
```c
/* 等待prvTask2()的通知，进入阻塞 */  
ulTaskNotifyTake( pdTRUE, portMAX_DELAY);  
```
## 8. 时间函数
### 8.1 延时函数
```c
//函数原型 vTaskDelay()
//相对延时 延时时间可能跟时间设定时间不同 要考虑优先级
void vTaskDelay(const TickType_t xTicksToDelay ); /* 延迟时间长度 */
//示例：vTaskDelay(500);
//绝对延时 vTaskDelayUntil()
//参数一：存储任务上次处于非阻塞状态时刻的变量地址 
//参数二：周期性延迟时间
//注意：使用此函数需要在 FreeRTOSConfig.h 配置文件中配置如下宏定义为 1
// #define INCLUDE_vTaskDelayUntil 1
void vTaskDelayUntil( TickType_t *pxPreviousWakeTime, 
					  const TickType_t xTimeIncrement );
//示例：
//设置存储系统时间的变量
TickType_t xLastWakeTime;
//获取当前系统时间
xLastWakeTime = xTaskGetTickCount();
/* vTaskDelayUntil是绝对延迟，vTaskDelay是相对延迟。*/
vTaskDelayUntil(&xLastWakeTime, 100);
```
### 8.2 获取时钟
```c
//在任务中获取时间节拍数
volatile TickType_t xTaskGetTickCount( void );
//在中断中获取时间节拍数
volatile TickType_t xTaskGetTickCountFromISR( void );
```

## 9. 事件标志组

### 注意事项

```c
/*
同时要在 FreeRTOSConfig.h
文件中使能如下三个宏定义：
#define INCLUDE_xEventGroupSetBitFromISR 1
#define configUSE_TIMERS 1
#define INCLUDE_xTimerPendFunctionCall 1
*/
```

### 9.1 创建事件标志组
```c
//创建事件标志组 内存自动分配
//返回值为NULL创建失败
EventGroupHandle_t xEventGroupCreate( void )
//创建事件标志组 内存自己分配
//参数：指向一个StaticEventGroup_t类型的变量，用来保存时间组结构体
//返回值为NULL创建失败    
EventGroupHandle_t xEventGroupCreateStatic( StaticEventGroup_t *pxEventGroupBuffer )
//示例：
//创建事件标志组句柄    
EventGroupHandle_t EventGroupHandle;    // 事件标志组句柄
//创建事件标志组
EventGroupHandle =  xEventGroupCreate(); 
if(NULL == EventGroupHandle)
{
    printf("创建任务事件组失败");
}
```

### 9.2 设置标志位

```c
/*
标志位代表某个事件的状况
*/
//示例：
// 定义事件位
#define BIT_0    (1<<0)
#define BIT_1    (1<<1)
#define BIT_2    (1<<2)   //后面还有其他事件可以此类推加最多24
```

### 9.3 设置事件位

```c
/*
所有设置事件位函数成功返回：pdPASS
　　            失败返回：pdFALSE：
*/
```



#### 9.3.1任务函数清零

```c
//函数原型：
//参数一：事件标志组句柄
//参数二：置零的事件位
EventBits_t xEventGroupClearBits( EventGroupHandle_t xEventGroup, 
                                  const EventBits_t uxBitsToClear )
//示例：
//将事件标志组中的BIT_0 置零
xEventGroupSetBits(EventGroupHandle,BIT_0);
```

#### 9.3.2 中断清零

```c
//函数原型：
//参数一：事件标志组句柄
//参数二：置零的事件位
BaseType_t xEventGroupClearBitsFromISR( EventGroupHandle_t xEventGroup, 
                                        const EventBits_t uxBitsToSet ) 
//示例：
//将事件标志组中的BIT_0 置零
xEventGroupClearBitsFromISR(EventGroupHandle,BIT_1);
```

#### 9.3.3任务函数置一

```c
//函数原型：
//参数一：事件标志组句柄
//参数二：置一的事件位
EventBits_t xEventGroupSetBits( EventGroupHandle_t xEventGroup, /* 事件标志组句柄 */
								const EventBits_t uxBitsToSet ); /* 事件标志位设置 */
//示例：
//将事件标志组中的BIT_0 置一
xEventGroupSetBits(EventGroupHandle,BIT_2);
```

#### 9.3.4中断置一

```c
//函数原型：
//参数一：事件标志组句柄
//参数二：置一的事件位
//参数三：标记退出此函数以后是否进行任务切换
BaseType_t xEventGroupSetBitsFromISR( EventGroupHandle_t xEventGroup, 
                                      const EventBits_t uxBitsToSet, 
                                      BaseType_t *pxHigherPriorityTaskWoken )
//示例：
//将事件标志组中的BIT_0 置一
BaseType_t pxHigherPriorityTaskWoken = pdFALSE;
BaseType_t xResult;
xResult = xEventGroupSetBitsFromISR(EventGroupHandle,BIT_2,&pxHigherPriorityTaskWoken);
if(xResult == pdPASS)
{
    //如果发送成功 判断是否有高优先级任务准备就绪，如果有则高优先级
    portYLELD_FROM_ISR(pxHigherPriorityTaskWoken);
}
```

### 9.4 获取事件标志组值

#### 9.4.1任务函数获取

```c
//函数原型
EventBits_t xEventGroupGetBits( EventGroupHandle_t xEventGroup )
//示例：
//创建存储变量
EventBits_t uxBits;
uxBits = xEventGroupGetBits(EventGroupHandle);
```

#### 9.4.2 中断中获取

```c
//任务原型
EventBits_t xEventGroupGetBitsFromISR( EventGroupHandle_t xEventGroup )
//示例：
//创建存储变量
EventBits_t uxBits;
uxBits = xEventGroupGetBitsFromISR(EventGroupHandle);
```

### 9.5 等待事件

```c
//函数原型： 只能在任务函数中调用
//参数二：标志位可设置多个用或例如：BIT_0 | BIT_1
//参数三：等待结束是否将等待的标志位置零 置零则 pdTRUE 不置零则 pdFALSE
//参数四：是否等待所有的标志位被设置 等待则所有事件触发才等待成功 与或 pdFALSE为不等待有一个事件触发则返回 pdTRUE则等待所有事件触发
//参数五：等待时间portMAX_DELAY则一直等待
//返回值：为触发的事件
EventBits_t xEventGroupWaitBits(const EventGroupHandle_t xEventGroup, /* 事件标志组句柄 */
                                const EventBits_t uxBitsToWaitFor, /* 等待被设置的事件标志位 */
                                const BaseType_t xClearOnExit, /* 选择是否清零被置位的事件标志位 */
                                const BaseType_t xWaitForAllBits, /* 选择是否等待所有标志位都被设置 */
                                TickType_t xTicksToWait ); /* 设置等待时间 */
//示例：
//定义存储触发事件的变量
EventBits_t uxBits;
//等待事件函数
//等待EventGroupHandle中的BIT_0或者BIT_1触发 一直等待 触发以后则将该BIT位置零
uxBits = xEventGroupWaitBits(EventGroupHandle,
							BIT_0 | BIT_1,//等待bit0和bit1（与同步） 等待bit0或bit1置1（或同步）
							pdTRUE,
							pdFALSE,//或同步，只要其中一个bit置1，就直接返回
							portMAX_DELAY//一直等待
							);
//示例二：
//等待EventGroupHandle中的BIT_0和BIT_1触发 两个触发则返回 一直等待 触发以后则将该BIT位置零
uxBits = xEventGroupWaitBits(EventGroupHandle,
							BIT_0 | BIT_1,//等待bit0和bit1（与同步） 等待bit0或bit1置1（或同步）
							pdTRUE,
							pdTRUE,//与同步，所有置一，就直接返回
							portMAX_DELAY//一直等待
							);
```

