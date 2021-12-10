# DK16函数

[toc]

## 1.lora函数

### lora工作图

![1638858070(1)](E:\Records\资料图片\1638858070(1).jpg)

### 注意事项

```c
/*
1.lora的发送和接受产生的中断都是dio1 配置更改需要更改寄存器
2.发送结束需要延时一会否则会错误
3.睡眠模式后需要片选引脚才能唤醒 或者定时器唤醒
4.lora的配置要根据标准配置不能乱配置
5.sx1268的传输功率最大22 官方手册如下图
6.影响到接收是否正确的配置有 
**********
  global.sx1268.modulation_params.PacketType = SX126X_PACKET_TYPE_LORA; //传输类型
  global.sx1268.modulation_params.Params.LoRa.Bandwidth = SX126X_LORA_BW_250;/宽带设置
  global.sx1268.modulation_params.Params.LoRa.CodingRate = SX126X_LORA_CR_4_5;//编码速率
  global.sx1268.modulation_params.Params.LoRa.LowDatarateOptimize = 0;//速率优化开关
  global.sx1268.modulation_params.Params.LoRa.SpreadingFactor = LORA_SF7;

  global.sx1268.packet_params.PacketType = SX126X_PACKET_TYPE_LORA;//数据包类型
  global.sx1268.packet_params.Params.LoRa.HeaderType = SX126X_LORA_PACKET_IMPLICIT;//隐式头
  global.sx1268.packet_params.Params.LoRa.CrcMode = SX126X_LORA_CRC_ON;//CRC校验码
  global.sx1268.packet_params.Params.LoRa.PayloadLength = 49;//数据长度  
  global.sx1268.packet_params.Params.LoRa.InvertIQ = SX126X_LORA_IQ_NORMAL;/iq配置
  global.sx1268.packet_params.Params.LoRa.PreambleLength = 8;//前导码长度
**********
7.发送超时可能因为没有打开优化速率开关global.sx1268.modulation_params.Params.LoRa.LowDatarateOptimize = 0;
8.sx1268的数据长度影响接收，但sx1280的数据长度不影响接收但是需要配置数据包
9.lora如果两个芯片共用一个spi两个芯片的发送同一时间
*/
```

![1638931272(1)](E:\Records\资料图片\1638931272(1).jpg)

### 1.1 lora（sx1268）发送

#### 流程图

![1638856625(1)](E:\Records\资料图片\1638856625(1).jpg)

#### 1.1.1lora发送函数调用过程

```c
/*
1.初始化数据后发送消息队列让发送任务函数处理数据，发送的数据夹带着要发送的数据
2.task_lora_tx接收到数据后判断是否是发送，后调用sx126x_tx官方函数发送数据，然后等待是否发送成功
3.sx126x_tx发送产生发送中断判断是dio几的中断，然后发送消息队列给中断处理任务函数
4.中断处理任务函数接收到消息队列，再把数据发送给sx126x_dio1_process官方函数处理
5.sx126x_dio1_process判断是发送成功还是超时；发送成功调用tx_done函数发送失败调用timeout函数
6.tx_done成功则发送任务通知
7.task_lora_tx等待任务通知，等待时间自定，到时没有tx_done则不会收到任务通知则发送失败，发送失败调用sx126x_init()再初始化,
8.释放申请的空间调用vPortFree()函数，然后将指针指向NULL
9.发送成功必须延时2ms，否则数据会错乱
*/
```

#### 1.1.2发送函数
##### 1.1.2.1 初始化数据
```c
 msg_t msg;//定义数据结构体
 msg.buf_len = 49;//定义buf的长度
 msg.buf = pvPortMalloc(msg.buf_len);//申请内存
 msg.buf[0] = 0x77;//给数据
 xQueueSend(global.queue_handle_lora_tx, &msg, portMAX_DELAY);//往消息队列里发送数据
```
##### 1.1.2.2 lora发送数据任务函数
```c
void task_lora_tx_li(void *arg)
{
  BaseType_t ret_code;
  msg_t lora_tx_msg = {.buf = NULL};
  //等待任务通知，先初始化好lora再通知
  ulTaskNotifyTake(pdTRUE, portMAX_DELAY);
  for(;;)
  {
      //等待消息来
    if(pdTRUE == xQueueReceive (global.queue_handle_lora_tx, &lora_tx_msg, portMAX_DELAY))
    { 
        //判断buf是否为空，判断是不是接收到数据
      if(lora_tx_msg.buf != NULL)
      {
          //打印数据长度
        NRF_LOG_INFO("[task_lora_tx_li] Send data length: %d", lora_tx_msg.buf_len);
          //调用官方tx函数
        sx126x_tx(&global.sx1268, lora_tx_msg.buf, lora_tx_msg.buf_len, 55);
          //等待任务通知，等100ms，任务通知在tx_done函数中
        ret_code = ulTaskNotifyTake(pdTRUE, ms2ticks(100));
        /* 根据等待通知的结果，执行对应操作 */
        if (ret_code == 1)
        {
            //发送成功，延时一会释放cpu
           vTaskDelay(ms2ticks(2));
        }
        else
        {
            //超时
          NRF_LOG_INFO("SX1268 tx timeout.");
          //重新初始化一下
         sx126x_init(&global.sx1268);
        }
      }
        //设置为接收模式
       sx126x_rx(&global.sx1268, 0);
    }
      //判断buf为真嘛
    if (lora_tx_msg.buf)
    {
        //释放空间
      vPortFree(lora_tx_msg.buf);
      //buf指向null
      lora_tx_msg.buf = NULL;
    }
  }
}
```

##### 1.1.2.3 lora接收中断任务函数

```c
void task_lora_dio1_li(void *arg)
{
  msg_t msg;//申请结构体
  msg.buf = NULL;//buf指向空
  for(;;)
  {
      //等待中断函数发送的消息
    if(pdPASS == xQueueReceive (global.queue_lora_dio1_msg, &msg, portMAX_DELAY))
    {
        //把数据交给官方函数处理
      sx126x_dio1_process (&global.sx1268);
    }
  }
}
```

##### 1.1.2.4 sx126x_dio1_process官方处理函数

```c
void sx126x_dio1_process (sx126x_dev *dev)
{
  volatile uint16_t irq_status = 0;
  //获取中断状态
  irq_status = sx126x_get_irq_status (dev);
  //清除中断状态
  sx126x_clear_irq_status (
      dev, (SX126X_IRQ_RX_DONE | SX126X_IRQ_CRC_ERROR | SX126X_IRQ_RX_TX_TIMEOUT | SX126X_IRQ_TX_DONE)); // This handler only handle these IRQ
  //判断是不是rx模式
  if (SX126X_MODE_RX == dev->op_mode)
  {
    //判断是不是lora模式
    if (SX126X_PACKET_TYPE_LORA == dev->modulation_params.PacketType)
    {
      //判断rx持续打开没
      if (dev->rx_continuous == false)
      {
        //!< Update operating mode state to a value lower than \ref MODE_STDBY_XOSC
        dev->op_mode = SX126X_MODE_STDBY_RC;//待机模式  
      }
      //产生的中断是不是rx
      if ((irq_status & SX126X_IRQ_RX_DONE) == SX126X_IRQ_RX_DONE)
      {
        //判断校验码是否对
        if ((irq_status & SX126X_IRQ_CRC_ERROR) == SX126X_IRQ_CRC_ERROR)
        {
          if ((dev != NULL) && (dev->callbacks.rxError != NULL))
          {
            dev->callbacks.rxError (SX126X_IRQ_CRC_ERROR_CODE);
          }
        }
        else
        {
         	//rx连续是否没开启
          if (dev->rx_continuous == false)
          {
            //待机模式
            dev->op_mode = SX126X_MODE_STDBY_RC;
            sx126x_write_register ( dev, 0x0902, 0x00);
            sx126x_write_register ( dev, 0x0944, sx126x_read_register (dev, 0x0944) | (1 << 1));
           
          }
          //清空数据
          memset (dev->radio_rx_payload, 0, sizeof(dev->radio_rx_payload));
          //获取数据
          sx126x_get_payload (dev, dev->radio_rx_payload,
                              &dev->radio_rx_payload_len, 255);
          //得到包状态
          sx126x_get_packet_status (dev, &dev->packet_status);
          //得到滴答时钟
          dev->rx_done_tick = dev->dio1_tick;
          //判断是否为空
          if ((dev != NULL) && (dev->callbacks.rxDone != NULL))
          {
            //调用rxdone函数
            dev->callbacks.rxDone (dev->radio_rx_payload,
                                  dev->radio_rx_payload_len,
                                  dev->packet_status.Params.LoRa.RssiPkt,
                                  dev->packet_status.Params.LoRa.SnrPkt);
          }
        }
      }
      //判断状态是否是超时
      if ((irq_status & SX126X_IRQ_RX_TX_TIMEOUT) == SX126X_IRQ_RX_TX_TIMEOUT)
      {
        //判断是否为空
        if ((dev != NULL) && (dev->callbacks.rxTimeout != NULL))
        {
          //调用超时函数
          dev->callbacks.rxTimeout ();
        }
      }
    }
    else if (SX126X_PACKET_TYPE_GFSK == dev->modulation_params.PacketType)
    {
      //TODO
    }
  }
  //判断模式是不是tx
  else if (SX126X_MODE_TX == dev->op_mode)
  {
    //待机模式
    dev->op_mode = SX126X_MODE_STDBY_RC;
    //判断产生的中断是不是tx
    if ((irq_status & SX126X_IRQ_TX_DONE) == SX126X_IRQ_TX_DONE)
    {
      //tx_done滴答时钟等于dio1滴答时钟
      dev->tx_done_tick = dev->dio1_tick;
      //判断是否为空
      if ((dev != NULL) && (dev->callbacks.txDone != NULL))
      {
        //调用txdone函数
        dev->callbacks.txDone ();
      }
    }
    //判断产生的中断是否是tx_timeout
    if ((irq_status & SX126X_IRQ_RX_TX_TIMEOUT) == SX126X_IRQ_RX_TX_TIMEOUT)
    {
      //判断是否为空
      if ((dev != NULL) && (dev->callbacks.txTimeout != NULL))
      {
        //调用tx_timeout函数
        dev->callbacks.txTimeout ();
      }
    }
  }
}
```

##### 1.1.2.5 tx_done官方处理函数

```c
void lora_tx_done()
{
  //发送任务通知
  xTaskNotifyGive(global.task_handle_lora_tx_li);
  // 发送完成后进入sleep模式，降低功耗 启动时为热启动
  sx126x_set_sleep_warm_start(&global.sx1268);
  NRF_LOG_INFO("SX1268 tx done.");
}
```

##### 1.1.2.6 tx_timeout官方处理函数

```c
void lora_tx_timeout()
{
  // 发送完成后进入sleep模式，降低功耗，一般不会进入到这里
  sx126x_set_sleep_warm_start (&global.sx1268);
}
```



### 1.2lora（sx1268）接收

#### 流程图

![1638930317(1)](E:\Records\资料图片\1638930317(1).jpg)

#### 1.2.1lora发送函数调用过程

```c
/*
1.配置好中断，写中断处理函数
2.中断处理函数往消息队列发消息
3.中断处理任务函数接收到消息
4.获取当前时间，把消息扔给sx126x_dio1_process处理
5.成功则调用txDone，超时则调用txTimeout
6.然后设置为接收模式
7.如果tx_timeout则初始化一次
8.无论接收成功与否都要释放申请的buf内存
*/
```
#### 1.2.2 lora发送配置函数
##### 1.2.2.1中断配置
```c
nrfx_gpiote_in_config_t dio1_config = NRFX_GPIOTE_CONFIG_IN_SENSE_LOTOHI(true);
dio1_config.pull = GPIO_PIN_CNF_PULL_Pulldown;
APP_ERROR_CHECK(
    nrfx_gpiote_in_init(SX1268_DIO1_PIN, &dio1_config, gpio_irq_handle));
nrfx_gpiote_in_event_enable(SX1268_DIO1_PIN, true);

nrfx_gpiote_in_config_t dio2_config = NRFX_GPIOTE_CONFIG_IN_SENSE_LOTOHI(true);
dio2_config.pull = GPIO_PIN_CNF_PULL_Pulldown;
APP_ERROR_CHECK(
    nrfx_gpiote_in_init(SX1268_DIO2_PIN, &dio2_config, gpio_irq_handle));
nrfx_gpiote_in_event_enable(SX1268_DIO2_PIN, true);
```

##### 1.2.2.2 中断函数

```c
void gpio_irq_handle(nrfx_gpiote_pin_t pin, nrf_gpiote_polarity_t action)
{
  msg_t msg;
  msg.tick = xTaskGetTickCountFromISR();
  switch (pin)
  {
  case SX1268_DIO1_PIN:
    xQueueSendFromISR(global.queue_lora_dio1_msg, &msg, NULL);
    break;
  case SX1268_DIO2_PIN:
    xQueueSendFromISR(global.queue_lora_dio2_msg, &msg, NULL);
    break;
  }
}
```

### 1.3lora常用函数

```c
//初始化sx1268
bool sx126x_init(sx126x_dev *dev);
//sx1268 tx传输
void sx126x_tx(sx126x_dev *dev, uint8_t* data, uint16_t len, uint32_t timeout_ms);
//sx1268读取
void sx126x_rx(sx126x_dev *dev, uint32_t timeout_ms);
//如果是睡眠模式则唤醒
void sx126x_check_device_ready( sx126x_dev *dev );
//保存要发送到无线电缓冲区的有效载荷  
void sx126x_set_payload( sx126x_dev *dev, uint8_t *payload, uint8_t size );
//读取收到的有效载荷
uint8_t sx126x_get_payload( sx126x_dev *dev, uint8_t *payload, uint8_t *size, uint8_t maxSize );
//发送有效载荷
void sx126x_send_payload( sx126x_dev *dev, uint8_t *payload, uint8_t size, uint32_t timeout );
//设置睡眠热启动 配置保留配置保留
void sx126x_set_sleep_warm_start (sx126x_dev *dev);
//设置睡眠冷启动 重新创建
void sx126x_set_sleep_cold_start (sx126x_dev *dev);
//设置睡眠模式
void sx126x_set_sleep( sx126x_dev *dev, SX126X_SleepParams_t sleepConfig );
//设置待机模式 SX126X_STDBY_RC为待机模式
void sx126x_set_standby( sx126x_dev *dev, SX126X_RadioStandbyModes_t mode );
//读取寄存器中的值
void sx126x_read_registers( sx126x_dev *dev, uint16_t address, uint8_t *buffer, uint16_t size );
//指示是否使用DIO2控制射频开关  参数二在初始化配置的时候选择  
void sx126x_set_dio2_as_rf_switch_ctrl( sx126x_dev *dev, uint8_t enable );
//设置功率调节器的工作模式 不设置默认仅使用LDO意味着Rx或Tx电流加倍  
void sx126x_set_regulator_mode( sx126x_dev *dev, SX126X_RadioRegulatorMode_t mode );
//为传输和接收设置数据缓冲基地地址 默认发送读取地址都为0x00
void sx126x_set_buffer_base_address( sx126x_dev *dev, uint8_t txBaseAddress, uint8_t rxBaseAddress );
//设置传输参数 参数二发射功率配置时设置  参数三功率放大器斜坡时间
void sx126x_set_tx_params( sx126x_dev *dev, int8_t power, RadioRampTimes_t rampTime );
//设置中断寄存器 
void sx126x_set_dio_irq_params( sx126x_dev *dev, uint16_t irqMask, uint16_t dio1Mask, uint16_t dio2Mask, uint16_t dio3Mask );
//决定哪个中断将停止内部无线电rx定时器 fasle报头/同步字检测后定时器停止
void sx126x_set_stop_rx_timer_on_preamble_detect( sx126x_dev *dev, bool enable );
```

### 2.1lora （sx1280）的发送

```c
/*
注释：
数据可以通过信号量来传递给任务处理函数
*/
```

```c
//任务处理函数
//设置1280的lora数据长度为需要发送的数据长度
sx1280_1.packet_params.Params.LoRa.PayloadLength = msg.len;
//重新配置lora的数据包，主要是配置长度避免浪费时间 如果每次的长度都是初始化配置好的这两步可忽略
sx128x_set_packet_params(&sx1280_1, &sx1280_1.packet_params);
//发送数据包 sx1280TickTime参数需定义
sx128x_send_payload(&sx1280_1, msg.buf, msg.len, sx1280TickTime);
//等待发送完成 释放信号量
xSemaphoreTake(global.semphore_sx1280_1_tx_done, ms2ticks(1000));
```

```c
/*然后发送数据包会正常会触发TX中断*/
/*触发中断，发送消息给中断任务函数处理*/
```

```c
//获取中断事件
sx1280irq_status = sx128x_get_irq_status(&sx1280_1);
//获取最后接收到的包有效载荷长度
sx128x_get_packet_status(&sx1280_1, &sx1280_1.packet_status);
//清除中断事件
sx128x_clear_irq_status(&sx1280_1, SX128X_IRQ_RADIO_ALL);
```

```c
/*判断中断事件是否是写事件*/
((sx1280irq_status & SX128X_IRQ_TX_DONE) == SX128X_IRQ_TX_DONE)
```

```c
/*如果是写事件则释放信号量给任务处理函数表示写完了，然后再设置为读模式*/
xSemaphoreGive(global.semphore_sx1280_1_tx_done);
sx128x_set_rx(&sx1280_1, sx1280TickTime);
```

### 2.2 lora（sx1280）的接收

```c
//获取中断事件
sx1280irq_status = sx128x_get_irq_status(&sx1280_1);
//获取最后接收到的包有效载荷长度
sx128x_get_packet_status(&sx1280_1, &sx1280_1.packet_status);
//清除中断事件
sx128x_clear_irq_status(&sx1280_1, SX128X_IRQ_RADIO_ALL);
```

```c
/*判断中断事件是否是读事件*/
((sx1280irq_status & SX128X_IRQ_RX_DONE) == SX128X_IRQ_RX_DONE)
```

```c
/*因为不获取上一次接收数据包的缓存状态就会在现有的地址上覆盖，可能会导致数据丢失，
获取最后接收数据包缓存区状态可以保证不会出现数据丢失的情况*/
```

```c
/*获取最后接收的数据包缓冲区状态*/
//函数原型 参数二：数据包的长度  参数三：上次接收的数据包缓冲区地址指针
void sx128x_get_rx_nuffer_status (sx128x_dev *dev, uint8_t *payloadLength,
                              uint8_t *rxStartBufferPointer)
sx128x_get_rx_nuffer_status(&sx1280_1, &tempsize, &tempoffset);
```

```c
/*获取数据：函数原型*/
//参数二：获取到的数据的存放地址 参数三：指向获取到的数据长度的地址  参数四：最大的长度
uint8_t sx128x_get_payload (sx128x_dev *dev, uint8_t *buffer, uint8_t *size,
                            uint8_t maxSize)
//方法一：不固定存放地址，这样可以避免出现数据丢失
//参数二：数组中的tempoffset是上一步获取的值，这样就不会出现参数覆盖的现象
sx128x_get_payload(&sx1280_1, &payload_buff[tempoffset], &payload_buffer_size, 10);
//方法二：固定存放地址，这样如果数据过多会出现丢失现象
//参数二：存放数据的地址  参数四：数据的最大长度，这里可以使用上面获取到的数据包的长度或者是初始化时定义的数据长度
sx128x_get_payload(&sx1280_1, payload_buff, &payload_buffer_size, 10);
```

