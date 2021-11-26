# 目录

[toc]

## 1.任务调度

### 1.1声明任务句柄

```c
/*头文件*/
#include "task.h"
TaskHandle_t app_task1_handle=NULL;//要声明任务句柄
```

### 1.2创建任务

```c
/* 创建任务1 
app_task1，任务函数
"app_task1"，任务的名字
128，分配给任务1的内存（栈）大小，单位为字
7,优先级为7，数值不能超过（configMAX_PRIORITIES -1）
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

------



## 2.挂起恢复

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

****



## 3.临界区

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
// 参数 3 ： 允许阻塞时间
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
if(pdPASS == xQueueReceiveFromISR(xTaskhandle1W5500,&eSetBits, &xHigherPriorityTaskWoken ));
//例子二：
if(pdPASS == xQueueReceiveFromISR(xTaskhandle1W5500,&eSetBits, NULL ));
```

***

