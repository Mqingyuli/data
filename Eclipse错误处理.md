[toc]

# Eclipse错误处理

## 1.0xfffffffe错误

### 1.1错误现象

```c
/*
Dbug时跳转到0xffffffe位置
*/
```

![1638754191(1)](E:\Records\资料图片\1638754191(1).jpg)

### 1.2处理方法

```c
/*
将图1中的s140_nrf52_7.2.0_softdevice.hex文件放到图二中Jlink中上传即可
*/
```

![1638754310(1)](E:\Records\资料图片\1638754310(1).jpg)

![1638754825](E:\Records\资料图片\1638754825.png)

## 2. J-Link GDB Server failed

### 2.1错误现象：

![1638923508(1)](E:\Records\资料图片\1638923508(1).jpg)

### 2.2解决问题

```c
/*J-Link GDB服务器故障:  
无法连接到目标。 请检查目标器是否通电，丝带线是否插好。  
 
要了解更多细节，请参阅JLinkGDBServerCL.exe控制台。  
*/
//检查是否插好
```

