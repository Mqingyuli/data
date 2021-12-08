# SDK16函数

[toc]

## 1.lora函数

### lora工作图

![1638858070(1)](E:\Records\资料图片\1638858070(1).jpg)

### 注意事项

```c
/*
1.lora的发送和接受产生的中断都是dio1 配置更改需要更改寄存器
2.发送结束需要延时一会否则会错误
3.
*/
```



### 1.1lora发送

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
7.task_lora_tx等待任务通知，等待时间自定，到时没有tx_done则不会收到任务通知则发送失败，发送失败调用sx126x_init()再初始化,发送成功延时100ms
8.释放申请的空间调用vPortFree()函数
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
           vTaskDelay(ms2ticks(100));
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
  // 发送完成后进入sleep模式，降低功耗
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



### 1.2lora接收

#### 流程图

![1638845917(1)](E:\Records\资料图片\1638845917(1).jpg)

#### 1.2.1lora发送函数调用过程

```c
/*
1.配置好中断，写中断处理函数
2.中断处理函数往消息队列发消息
3.中断处理任务函数接收到消息
4.获取当前时间，把消息扔给sx126x_dio1_process处理
5.成功则调用rxDone，超时则调用rxTimeout
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

void sx126x_check_device_ready( sx126x_dev *dev );

/*!
   * \brief Saves the payload to be send in the radio buffer
   *
   * \param [in]  payload       A pointer to the payload
   * \param [in]  size          The size of the payload
   */
void sx126x_set_payload( sx126x_dev *dev, uint8_t *payload, uint8_t size );

/*!
   * \brief Reads the payload received. If the received payload is longer
   * than maxSize, then the method returns 1 and do not set size and payload.
   *
   * \param [out] payload       A pointer to a buffer into which the payload will be copied
   * \param [out] size          A pointer to the size of the payload received
   * \param [in]  maxSize       The maximal size allowed to copy into the buffer
   */
uint8_t sx126x_get_payload( sx126x_dev *dev, uint8_t *payload, uint8_t *size, uint8_t maxSize );
```

