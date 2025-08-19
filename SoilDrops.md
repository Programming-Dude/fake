# **SoilDrops 智能灌溉系统项目概览**

## **项目概述**

这是一个由另一个团队开发的**智能灌溉系统**，包含两个主要硬件组件：

### **1. 硬件架构**
- **CC1310 MCU 传感器和控制器**（德州仪器 CC1310 LaunchPad）
- **ESP32 网关/控制器**（乐鑫 ESP32）

### **2. 系统组件**

#### **CC1310 端（EBYTE modules and CC1310 MCU Source code/）**
CC1310 系统包含三个主要模块：

1. **传感器模块** (`Sensor/`)
   - 土壤湿度传感
   - 电池监控
   - 按钮接口
   - 卡尔曼滤波处理传感器数据
   - RF 无线通信

2. **控制器模块** (`Controller/`)
   - 主系统控制器
   - RF 数据包接收
   - UART 串口通信

3. **继电器模块** (`Relay/`)
   - 灌溉继电器控制
   - 系统时钟管理
   - RF 和 UART 通信

#### **ESP32 端（Esp32_source_code/）**
- **智能灌溉系统 v1.0.0**
- 包含组件：
  - ADC（模数转换）
  - Flash 闪存管理
  - MQTT 通信
  - OTA（空中升级）
  - UART 串口通信

### **3. 通信架构**
- **RF 通信**：CC1310 模块之间使用 EBYTE 模块进行无线通信
- **UART**：组件间的串口通信
- **MQTT**：通过 ESP32 实现互联网连接，用于云端/远程监控

### **4. 核心功能**
- **传感器数据采集**：土壤湿度监控，使用卡尔曼滤波
- **无线通信**：传感器和控制器之间的 RF 网状网络
- **云端连接**：MQTT 用于远程监控和控制
- **OTA 升级**：远程固件更新
- **电池管理**：传感器节点的电源监控

### **5. 系统工作流程**
这是一个**分布式物联网灌溉系统**，其中：
- **传感器**收集土壤湿度数据
- **控制器**处理和转发信息
- **继电器**控制灌溉阀门
- **ESP32** 作为互联网/云端的网关

### **6. 技术特点**
- 使用 TI-RTOS 实时操作系统
- 支持多节点 RF 网络
- 具备云端数据上传和远程控制能力
- 模块化设计，便于维护和扩展

这个系统设计用于**智能农业**应用，能够根据土壤湿度自动控制灌溉，并通过互联网实现远程监控和管理。

您希望我深入分析哪个特定组件？我可以详细分析：
1. 传感器数据采集和处理
2. RF 通信协议
3. ESP32 网关功能
4. 整体系统架构和数据流
5. 任何特定模块的代码结构



我来深入分析 CC1310 MCU 源代码目录的结构。让我先查看整体目录结构，然后逐步分析每个文件。

Ran tool
Read file: EBYTE modules and CC1310 MCU Source code/version.txt
Read file: EBYTE modules and CC1310 MCU Source code/ccfg.c
现在让我查看各个模块的目录结构：

Ran tool
Ran tool
# **CC1310 MCU 源代码深度分析 - 第一部分**

## **1. 项目基本信息**

### **开发环境**
- **IDE**: Code Composer Studio v12.8.1.00005
- **SDK**: SimpleLink CC13x0 SDK v4.20.02.07
- **固件版本**:
  - Sensor: v2.4
  - Controller: v2.2  
  - Booster: v2.0

### **重要配置说明**
- 需要在 IDE 中设置项目后更新 `ccfg.c` 文件
- 传感器模块：第197行设置为 `0xF1`
- 控制器和继电器模块：第197行设置为 `0xFE`

## **2. 核心配置文件分析**

### **ccfg.c (主配置文件)**
- **作用**: CC13x0 设备族的客户配置
- **功能**: 
  - 配置启动代码
  - 配置软件无线电栈
  - 配置芯片行为
- **关键设置**:
  - `CCFG_FORCE_VDDR_HH`: 强制 VDDR 电压到工厂 HH 设置
  - 影响输出功率和功耗

## **3. Sensor 模块结构分析**

### **目录结构**
```
Sensor/
├── APP/                    # 应用程序代码
├── Debug/                  # 编译输出
├── smartrf_settings/       # RF 设置
├── targetConfigs/          # 目标配置
├── main_senser.c          # 主程序
├── main_tirtos.c          # TI-RTOS 主程序
├── CC1310_LAUNCHXL.c      # 硬件抽象层
├── CC1310_LAUNCHXL.h      # 硬件定义
├── Board.h                # 板级支持包
├── RFQueue.c/h            # RF 队列管理
└── ccfg.c                 # 传感器专用配置
```

### **APP 目录核心文件**
1. **Sensor.c/h** - 传感器驱动
2. **Battery.c/h** - 电池监控
3. **Button.c/h** - 按钮控制
4. **Kalman.c/h** - 卡尔曼滤波
5. **main_process.c/h** - 主处理逻辑
6. **rf_app.c/h** - RF 通信应用
7. **uart_app.c/h** - UART 通信
8. **systick.c/h** - 系统时钟

## **4. 系统架构特点**

### **实时操作系统**
- 使用 **TI-RTOS** 实时操作系统
- 支持多任务并发处理
- 提供任务调度和中断管理

### **硬件抽象层**
- `CC1310_LAUNCHXL.c/h` 提供硬件抽象
- `Board.h` 定义板级配置
- 支持不同硬件配置

### **通信架构**
- **RF 通信**: 使用 RFQueue 管理数据包
- **UART 通信**: 串口数据交换
- **传感器接口**: 模拟和数字信号处理

由于内容较多，我需要继续分析其他模块。您希望我继续分析 Controller 和 Relay 模块吗？还是先深入分析 Sensor 模块的具体代码实现？



好的，我继续分析 Controller 和 Relay 模块，然后深入分析 Sensor 模块的具体代码实现。

Ran tool
Ran tool
Ran tool
Ran tool
现在让我深入分析一些关键代码文件：

Read file: EBYTE modules and CC1310 MCU Source code/Sensor/main_senser.c
Read file: EBYTE modules and CC1310 MCU Source code/Controller/main_controller.c
Read file: EBYTE modules and CC1310 MCU Source code/Relay/main_booster.c
现在让我分析一些关键的应用程序文件：

Read file: EBYTE modules and CC1310 MCU Source code/Sensor/APP/main_process.c
Read file: EBYTE modules and CC1310 MCU Source code/Sensor/APP/Sensor.c
# **CC1310 MCU 源代码深度分析 - 第二部分**

## **5. 三个模块的详细分析**

### **A. Sensor 模块 (传感器模块)**

#### **主要功能**
- **土壤湿度检测**: 使用 ADC 读取模拟信号
- **电池监控**: 实时监测电池电量
- **数据采集**: 收集传感器数据并打包
- **RF 通信**: 发送数据到控制器

#### **核心文件分析**

**main_senser.c**
```c
void *senser_main(void *arg0)
{
    nvs_config();           // 非易失性存储配置
    button_config();        // 按钮配置
    Init_Process();         // 初始化处理
    sleep(3);
    Get_Battery();          // 获取电池状态
    Enter_Tx(data_buf,5);   // 发送初始数据
    
    while(1) {
        Loop_Process();     // 主循环处理
        count++;
        if(count == 3600/wakeup_duration || wakeup_duration>3600) {
            Get_Battery();  // 定期检查电池
            count=0;
        }
    }
}
```

**Sensor.c - 土壤湿度检测**
```c
// 湿度等级定义 (基于 1000k 电阻配置)
#define Moisture_open   3610000  // 开路状态
#define Moisture_10inch 2965000  // 10英寸深度
#define Moisture_25inch 2628000  // 25英寸深度
#define Moisture_45inch 2048000  // 45英寸深度
#define Moisture_55inch 1971000  // 55英寸深度
#define Moisture_3inch  1809400  // 3英寸深度

uint8_t Get_moisture_Level(uint32_t Moisture_level)
{
    // 根据 ADC 值计算湿度百分比 (0-100%)
    // 使用分段线性插值算法
}
```

### **B. Controller 模块 (控制器模块)**

#### **主要功能**
- **RF 数据接收**: 接收传感器数据
- **UART 转发**: 将数据转发到 ESP32
- **数据中继**: 作为传感器和 ESP32 之间的桥梁

#### **核心文件分析**

**main_controller.c**
```c
void *controller_main(void *arg0)
{
    RF_init();    // 初始化 RF
    Uart_init();  // 初始化 UART
    
    while(1) {
        if(rf_flag==1) {           // RF 数据接收标志
            rf_flag=0;
            Uart_send(uartbuffer,uart_index);  // 发送到 UART
            uart_index = 0;
            Task_sleep(500000/Clock_tickPeriod);
            Enter_Rx();             // 重新进入接收模式
            counter1 = 0;
        }

        if(Uart_RX_Done == true) {  // UART 接收完成
            Uart_RX_Done = false;
            Uart_send(buffer, counter);
            Enter_Tx(buffer, counter);  // 通过 RF 转发
            memset(buffer,0,sizeof(buffer));
            counter=0;
            counter1 = 0;
        }
        
        // 定期发送心跳包
        if(counter1 >= 1800000000) {
            Enter_Tx(temp_buffer, 7);
            Enter_Rx();
        }
    }
}
```

### **C. Relay 模块 (继电器模块)**

#### **主要功能**
- **继电器控制**: 控制灌溉阀门
- **系统时钟**: 使用 systick 定时器
- **数据验证**: 防止重复数据包

#### **核心文件分析**

**main_booster.c**
```c
void *controller_main(void *arg0)
{
    RF_init();
    Uart_init();
    Init_sysTick();    // 初始化系统时钟
    Start_sysTick();   // 启动系统时钟
    
    while(1) {
        if(rf_flag==1) {
            rf_flag = 0;
            
            // 状态切换逻辑
            if(uartbuffer[11] == 0) {
                uartbuffer[11] = 1;
            } else if(uartbuffer[11] == 1) {
                uartbuffer[11] = 2;
            }
            
            // 数据验证和防重复机制
            if(uart_index >= 12) {
                if(uartbuffer[4] == last_payload[4] && 
                   uartbuffer[5] == last_payload[5] && 
                   uartbuffer[6] == last_payload[6] && 
                   uartbuffer[7] == last_payload[7]) {
                    
                    Systick_count = sysTick(false);
                    if(Systick_count >= 10) {  // 10秒延迟
                        Uart_send(uartbuffer,uart_index);
                        Enter_Tx();
                        Stop_sysTick();
                        Systick_count = sysTick(true);
                    }
                } else {
                    // 新数据立即处理
                    Uart_send(uartbuffer,uart_index);
                    Enter_Tx();
                    Stop_sysTick();
                    Systick_count = sysTick(true);
                }
                
                memcpy(last_payload, uartbuffer, uart_index);
                memset(uartbuffer, 0, 100);
                uart_index = 0;
                Start_sysTick();
                Enter_Rx();
            }
        }
    }
}
```

## **6. 系统通信协议分析**

### **RF 数据包格式**
```c
// 数据包结构 (来自 main_process.c)
buf[0] = 'E';    // 包头标识
buf[1] = 'W';    // 设备类型
buf[2] = 'W';    // 子类型
buf[3] = serial[0];  // 序列号
buf[4] = serial[1];
buf[5] = serial[2];
buf[6] = serial[3];
buf[7] = serial[4];
buf[8] = Len_in;     // 数据长度
buf[9] = batt_level; // 电池电量
// buf[10] 开始是实际数据
```

### **工作流程**
1. **传感器** → 采集土壤湿度 → 打包数据 → RF 发送
2. **控制器** → 接收 RF 数据 → UART 转发到 ESP32
3. **继电器** → 接收控制命令 → 控制灌溉阀门

## **7. 关键技术特点**

### **功耗管理**
- 使用 `wakeup_duration` 控制唤醒周期
- 定期检查电池电量
- 低功耗模式设计

### **数据可靠性**
- 卡尔曼滤波处理传感器数据
- 防重复数据包机制
- 心跳包保持连接

### **实时性**
- TI-RTOS 任务调度
- 中断驱动的 RF 通信
- 系统时钟精确控制

这个系统设计非常完整，体现了专业的嵌入式系统开发水平。您希望我继续深入分析某个特定方面吗？


我来深入分析 Controller 目录的每个文件和组件，逐步推理其功能和设计逻辑。

Read file: EBYTE modules and CC1310 MCU Source code/Controller/README.md
Read file: EBYTE modules and CC1310 MCU Source code/Controller/APP/rf_app.h
Read file: EBYTE modules and CC1310 MCU Source code/Controller/APP/rf_app.c
Read file: EBYTE modules and CC1310 MCU Source code/Controller/APP/rf_app.c
Read file: EBYTE modules and CC1310 MCU Source code/Controller/APP/uart_app.h
Read file: EBYTE modules and CC1310 MCU Source code/Controller/APP/uart_app.c
Read file: EBYTE modules and CC1310 MCU Source code/Controller/Board.h
Read file: EBYTE modules and CC1310 MCU Source code/Controller/RFQueue.h
Read file: EBYTE modules and CC1310 MCU Source code/Controller/smartrf_settings/smartrf_settings.h
# **Controller 模块深度分析 - 逐步推理**

## **1. 模块概述和设计目标**

### **Controller 的核心作用**
Controller 模块是整个智能灌溉系统的**数据中继中心**，主要功能是：
- 接收来自 Sensor 模块的 RF 数据
- 将数据转发给 ESP32 网关
- 接收 ESP32 的控制命令并转发给 Relay 模块

### **设计理念**
这是一个典型的**星型网络架构**中的中心节点，采用**双向通信**模式。

## **2. 文件结构分析**

### **核心文件层次**
```
Controller/
├── main_controller.c          # 主程序逻辑
├── APP/
│   ├── rf_app.c/h            # RF 通信应用层
│   └── uart_app.c/h          # UART 通信应用层
├── Board.h                   # 硬件抽象层
├── RFQueue.c/h               # RF 队列管理
└── smartrf_settings/         # RF 参数配置
```

## **3. 主程序逻辑分析 (main_controller.c)**

### **程序流程推理**
```c
void *controller_main(void *arg0)
{
    RF_init();    // 初始化 RF 模块
    Uart_init();  // 初始化 UART 模块
    
    while(1) {
        // 1. RF 数据接收处理
        if(rf_flag==1) {
            rf_flag=0;
            Uart_send(uartbuffer,uart_index);  // 转发到 ESP32
            uart_index = 0;
            Task_sleep(500000/Clock_tickPeriod);  // 500ms 延迟
            Enter_Rx();  // 重新进入接收模式
            counter1 = 0;
        }

        // 2. UART 数据接收处理
        if(Uart_RX_Done == true) {
            Uart_RX_Done = false;
            Uart_send(buffer, counter);  // 回显确认
            Enter_Tx(buffer, counter);   // RF 转发给 Relay
            memset(buffer,0,sizeof(buffer));
            counter=0;
            counter1 = 0;
        }

        // 3. 心跳机制
        Task_sleep(1/Clock_tickPeriod);
        counter1++;
        if(counter1 >= 1800000000) {  // 约30秒
            Enter_Tx(temp_buffer, 7);  // 发送心跳包
            Enter_Rx();
        }
    }
}
```

### **关键设计推理**

#### **A. 数据流向控制**
- **RF → UART**: 传感器数据转发到 ESP32
- **UART → RF**: ESP32 控制命令转发到继电器
- **双向中继**: 实现完整的双向通信链路

#### **B. 时序控制机制**
- **500ms 延迟**: 确保 UART 发送完成
- **30秒心跳**: 保持网络连接活跃
- **任务调度**: 使用 TI-RTOS 的任务延迟

## **4. RF 通信层分析 (rf_app.c)**

### **RF 初始化配置**
```c
void RF_init(void)
{
    // 1. 数据队列配置
    RFQueue_defineQueue(&dataQueue, rxDataEntryBuffer, 
                       sizeof(rxDataEntryBuffer), NUM_DATA_ENTRIES, 
                       MAX_LENGTH + NUM_APPENDED_BYTES);
    
    // 2. 接收参数配置
    RF_cmdPropRx.pQueue = &dataQueue;
    RF_cmdPropRx.rxConf.bAutoFlushIgnored = 1;    // 自动丢弃忽略的包
    RF_cmdPropRx.rxConf.bAutoFlushCrcErr = 1;     // 自动丢弃 CRC 错误包
    RF_cmdPropRx.maxPktLen = MAX_LENGTH;          // 最大包长度 30 字节
    RF_cmdPropRx.pktConf.bRepeatOk = 1;           // 允许重复包
    RF_cmdPropRx.pktConf.bRepeatNok = 1;          // 允许重复错误包
    RF_cmdPropRx.rxConf.bAppendRssi = 1;          // 附加 RSSI 信息
    
    // 3. 启动接收模式
    RF_postCmd(rfHandle, (RF_Op*)&RF_cmdPropRx, RF_PriorityNormal, 
               &callback, RF_EventRxEntryDone);
}
```

### **数据包处理逻辑**
```c
void callback(RF_Handle h, RF_CmdHandle ch, RF_EventMask e)
{
    if (e & RF_EventRxEntryDone) {
        // 1. 获取数据包
        currentDataEntry = RFQueue_getDataEntry();
        packetLength = *(uint8_t*)(&currentDataEntry->data);
        packetDataPointer = (uint8_t*)(&currentDataEntry->data + 1);
        
        // 2. 数据包验证
        if(packetLength >= 12) {
            memcpy(packet, packetDataPointer, (packetLength));
            // 验证包头: 'E' + 'W' + 'W'
            if(packet[0] == 'E' && packet[1] == 'W' && packet[2] == 'W') {
                memcpy(uartbuffer, packetDataPointer, uart_index);
                rf_flag=1;  // 设置接收标志
            }
        }
        RFQueue_nextEntry();  // 移动到下一个数据条目
    }
}
```

### **关键设计推理**

#### **A. 数据包格式验证**
- **最小长度**: 12 字节确保数据完整性
- **包头验证**: 'EWW' 标识符防止数据混乱
- **长度检查**: 防止缓冲区溢出

#### **B. 错误处理机制**
- **CRC 错误**: 自动丢弃错误数据包
- **重复包**: 允许重复包提高可靠性
- **缓冲区管理**: 使用队列防止数据丢失

## **5. UART 通信层分析 (uart_app.c)**

### **UART 配置参数**
```c
void Uart_init(void)
{
    uartParams.writeDataMode = UART_DATA_BINARY;    // 二进制模式
    uartParams.readDataMode = UART_DATA_BINARY;     // 二进制模式
    uartParams.readReturnMode = UART_RETURN_FULL;   // 完整返回
    uartParams.readEcho = UART_ECHO_OFF;            // 关闭回显
    uartParams.baudRate = 115200;                   // 波特率 115200
    uartParams.readMode = UART_MODE_CALLBACK;       // 回调模式
    uartParams.readCallback = Uart_cb;              // 接收回调
    uartParams.writeMode = UART_MODE_CALLBACK;      // 发送回调模式
    uartParams.writeCallback = uart_TX_callback;    // 发送回调
}
```

### **命令处理逻辑**
```c
void Uart_cb(UART_Handle handle, void *buf, size_t count)
{
    // 1. 状态查询命令
    if(buff_Counter > 5) {
        if(buffer_status[0] == 's' && buffer_status[1] == 't' && 
           buffer_status[2] == 'a' && buffer_status[3] == 't' && 
           buffer_status[4] == 'u' && buffer_status[5] == 's') {
            char buff_responce[5] = {'D','o','n','e','\0'};
            UART_write(uart, buff_responce, 5);
            buff_Counter = 0;
        }
    }
    
    // 2. 版本查询命令
    if(buff_Counter_2 > 6) {
        if(buffer_status_2[0] == 'v' && buffer_status_2[1] == 'e' && 
           buffer_status_2[2] == 'r' && buffer_status_2[3] == 's' && 
           buffer_status_2[4] == 'i' && buffer_status_2[5] == 'o' && 
           buffer_status_2[6] == 'n') {
            char ver_res[20] = {0};
            sprintf(ver_res, "version=%s\0", Version);  // Version = "2.2"
            UART_write(uart, ver_res, strlen(ver_res));
            buff_Counter_2 = 0;
        }
    }
    
    // 3. 控制命令处理
    if(counter >= 11) {
        if(buffer[0] == 'A' && buffer[1] == 'C' && buffer[2] == 'K') {
            Uart_RX_Done = true;  // ACK 确认命令
        } else if(buffer[0] == 'S' && buffer[1] == 'L' && 
                  buffer[2] == 'E' && buffer[3] == 'E' && 
                  buffer[4] == 'P') {
            Uart_RX_Done = true;  // SLEEP 睡眠命令
        } else {
            counter = 0;
            memset(buffer, 0, sizeof(buffer));
        }
    }
}
```

### **关键设计推理**

#### **A. 命令协议设计**
- **状态查询**: `status` → `Done`
- **版本查询**: `version` → `version=2.2`
- **控制命令**: `ACK` 或 `SLEEP`

#### **B. 缓冲区管理**
- **多缓冲区**: 分别处理不同类型命令
- **长度检查**: 防止命令解析错误
- **状态机**: 使用计数器跟踪命令状态

## **6. 系统架构推理**

### **通信协议栈**
```
应用层:    命令处理 (ACK/SLEEP/status/version)
传输层:    UART (115200 bps) + RF (433MHz)
网络层:    数据包路由和转发
物理层:    CC1310 硬件接口
```

### **数据流设计**
```
Sensor → RF → Controller → UART → ESP32
ESP32 → UART → Controller → RF → Relay
```

### **可靠性机制**
1. **数据包验证**: 包头检查和长度验证
2. **错误处理**: CRC 检查和自动丢弃
3. **心跳机制**: 定期发送保持连接
4. **缓冲区管理**: 防止数据溢出

## **7. 设计优势分析**

### **A. 模块化设计**
- RF 和 UART 功能分离
- 清晰的接口定义
- 易于维护和扩展

### **B. 实时性保证**
- 中断驱动的数据接收
- 回调机制避免阻塞
- TI-RTOS 任务调度

### **C. 可靠性设计**
- 多重错误检查
- 自动重传机制
- 状态监控和恢复

这个 Controller 模块设计非常专业，体现了成熟的嵌入式系统开发经验，特别是在通信协议设计和错误处理方面。



