# 二、HTTP 通讯（续）

## 2.5 数据读写接口

通过数据读写接口可以通过网关读取、写入机台数据，或读取机组数据。数据读写接口分为两类：**直接读写**与**缓存读取**。

---

### 2.5.1 直接读写

使用直接读写类接口，网关会立刻与目标机台通讯，获取或写入数据。

---

### 2.5.1.1 readAlarm 读取警报信息

```
GET  /api/cnc/readAlarm?machineID=MACHINEID
```

**请求参数**

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| machineID | String | （必需）目标机台标识，详见 1.1.1 machineID 机台标识 |

**返回示例**

```json
{
  "alarmMsg": [
    "警报 1",
    "警报 2"
  ],
  "alarmLevel": [
    "WRN",
    "WRN"
  ]
}
```

**返回参数**

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| alarmMsg | String[] | （必需）机台当前警报内容。如无警报，返回空 Array |
| alarmLevel | String[] | （必需）警报级别，与警报内容按顺序对应。如无警报，返回空 Array。详见表格 48 警报级别 |

> 注：从 1.8.1 版本开始，警报时间被移除。如需获取机台警报的起始时间，可使用接口 2.7.1.2 alarm 机台警报数据分析。

---

### 2.5.1.2 readCNCStatus 读取机台状态

```
GET  /api/cnc/readCNCStatus?machineID=MACHINEID&channel=CHANNEL
```

**请求参数**

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| machineID | String | （必需）目标机台标识，详见 1.1.1 machineID 机台标识 |
| channel | Int32 | 机台通道号，如不补充则默认为 0，即主通道 |

**返回示例**

```json
{
  "cncStatus": "MANUAL_EDIT",
  "alarmStatus": "ALARM",
  "alarmLevel": "WRN",
  "channel": 2
}
```

**返回参数**

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| cncStatus | String | （必需）运行状态，详见表格 44 运行状态 |
| alarmStatus | String | （必需）警报状态，详见表格 45 警报状态 |
| alarmLevel | String | 警报级别，如有多个警报，取这些警报中的最高级别。如无警报，则不返回。详见表格 48 警报级别 |
| channel | Int32 | 机台通道号，仅当请求中补充 channel 且不为 0 时出现 |

> 注：由网关后处理得到的修正运行状态 adjustedStatus，累计关机时间 offTime，累计待机时间 waitTime，累计急停时间 emergencyTime，累计自动运行时间 autoRunTime，累计调机时间 ManualTime 等，暂不支持 HTTP 协议。需要在开启自动采集任务后，通过接口 2.5.2.1 readTaskData 或 2.5.2.2 batchReadTaskData，或通过 MODBUS、MQTT、数据库等方式获取。

---

### 2.5.1.3 readCNCStatusDetails 读取机台状态详情

此接口除了读取到与 2.5.1.2 readCNCStatus 读取机台状态相同的通用机台状态数据，还能获取到不通用的原始数据。

```
GET  /api/cnc/readCNCStatusDetails?machineID=MACHINEID&channel=CHANNEL
```

**请求参数**

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| machineID | String | （必需）目标机台标识，详见 1.1.1 machineID 机台标识 |
| channel | Int32 | 机台通道号，如不补充则默认为 0，即主通道 |

**返回示例**

```json
{
  "mode": "AUTO_RUN",
  "programStatus": "RUNNING",
  "emergencyStatus": "NOT_EMG",
  "dryRunStatus": "DRY_RUN",
  "cncStatus": "AUTO_RUN",
  "alarmStatus": "ALARM",
  "alarmLevel": "WRN",
  "channel": 2
}
```

**返回参数**

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| mode | String | 运行模式 |
| programStatus | String | 程序状态 |
| emergencyStatus | String | 急停状态，见表格 46 急停状态 |
| dryRunStatus | String | 试运行状态 |
| cncStatus | String | （必需）运行状态，详见表格 44 运行状态 |
| alarmStatus | String | （必需）警报状态，详见表格 45 警报状态 |
| alarmLevel | String | 警报级别，如有多个警报取最高级别。如无警报则不返回。详见表格 48 警报级别 |
| channel | Int32 | 机台通道号，仅当请求中补充 channel 且不为 0 时出现 |

---

### 2.5.1.4 readCount 读取加工计数

```
GET  /api/cnc/readCount?machineID=MACHINEID
```

**请求参数**

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| machineID | String | （必需）目标机台标识，详见 1.1.1 machineID 机台标识 |

**返回示例**

```json
{
  "count": 1052
}
```

**返回参数**

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| count | Int32 | （必需）从机台系统获取的加工计数 |

> 注：由网关统计生成的机台累计加工计数 cumulativeCount，与当前监控时间内的加工计数 currentCount 暂不支持 HTTP 协议。需要在开启自动采集任务后，通过接口 2.5.2.1 readTaskData 或 2.5.2.2 batchReadTaskData，或通过 MODBUS、MQTT、数据库等方式获取。

---

### 2.5.1.5 readCurrentToolNumber 读取当前刀号

```
GET  /api/cnc/readCurrentToolNumber?machineID=MACHINEID&channel=CHANNEL
```

**请求参数**

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| machineID | String | （必需）目标机台标识，详见 1.1.1 machineID 机台标识 |
| channel | Int32 | （机台通道号，如不补充则默认为 0，即主通道 |

**返回示例**

```json
{
  "toolNumber": "6",
  "toolOffsetNum": "6",
  "channel": 2
}
```

**返回参数**

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| toolNumber | String | （必需）当前刀号 |
| toolOffsetNum | String | 当前刀补号 |
| channel | Int32 | 机台通道号，仅当请求中补充 channel 且不为 0 时出现 |

---

### 2.5.1.6 readEnergyConsum 读取能耗数据

目前支持部分款 Brother 兄弟机型和模拟机台。

```
GET  /api/cnc/readEnergyConsum?machineID=MACHINEID
```

**请求参数**

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| machineID | String | （必需）目标机台标识，详见 1.1.1 machineID 机台标识 |

**返回示例**

```json
{
  "allServoAxes": 127144.0,
  "coolantPump": 41577.0,
  "ctsPump": 6507.0,
  "chipFlusherPump": 0.0,
  "cyclonePump": 0.0,
  "system24V": 46270.0,
  "ncControl": 10099.0,
  "lcdBacklight": 2884.0,
  "chipConveyor": 0.0,
  "screwConveyor": 0.0,
  "autoLubrication": 239.0,
  "spindleCoolingFan": 7212.0,
  "servoControl": 23806.0
}
```

**返回参数**

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| allServoAxes | Double | 所有伺服轴能耗[Wh] |
| coolantPump | Double | 冷却液泵能耗[Wh] |
| ctsPump | Double | CTS 泵能耗[Wh] |
| chipFlusherPump | Double | 切屑冲洗器泵能耗[Wh] |
| cyclonePump | Double | 离心式过滤器用泵能耗[Wh] |
| system24V | Double | 24V 系统能耗[Wh] |
| ncControl | Double | NC 控制能耗[Wh] |
| lcdBacklight | Double | LCD 背光能耗[Wh] |
| chipConveyor | Double | 排屑器能耗[Wh] |
| screwConveyor | Double | 螺旋排屑器能耗[Wh] |
| autoLubrication | Double | 自动注油设备（或自动注脂设备）能耗[Wh] |
| spindleCoolingFan | Double | 主轴冷却风扇能耗[Wh] |
| servoControl | Double | 伺服控制能耗[Wh] |

---

### 2.5.1.7 readFeed 读取进给数据

仅限激光切割机/焊接机。

```
GET  /api/cnc/readFeed?machineID=MACHINEID&channel=CHANNEL
```

**请求参数**

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| machineID | String | （必需）目标机台标识，详见 1.1.1 machineID 机台标识 |
| channel | Int32 | 机台通道号，如不补充则默认为 0，即主通道 |

**返回示例**

```json
{
  "overFeed": 100.0,
  "actFeed": 80.0,
  "overRapid": 100.0,
  "channel": 2
}
```

**返回参数**

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| overFeed | Float | 进给倍率[%] |
| actFeed | Float | 实际进给速率 |
| overRapid | Float | 快速倍率[%] |
| channel | Int32 | 机台通道号，仅当请求中补充 channel 且不为 0 时出现 |

---

### 2.5.1.8 readFeedAndSpindle 读取进给转速数据

仅限 CNC。

```
GET  /api/cnc/readFeedAndSpindle?machineID=MACHINEID&channel=CHANNEL
```

**请求参数**

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| machineID | String | （必需）目标机台标识，详见 1.1.1 machineID 机台标识 |
| channel | Int32 | 机台通道号，如不补充则默认为 0，即主通道 |

**返回示例**

```json
{
  "overSpindle": 150.0,
  "actSpindle": 3000.0,
  "overFeed": 100.0,
  "actFeed": 80.0,
  "overRapid": 100.0,
  "channel": 2
}
```

**返回参数**

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| overSpindle | Float | 主轴转速倍率[%] |
| actSpindle | Float | 实际主轴转速 |
| overFeed | Float | 进给倍率[%] |
| actFeed | Float | 实际进给速率 |
| overRapid | Float | 快速倍率[%] |
| channel | Int32 | 机台通道号，仅当请求中补充 channel 且不为 0 时出现 |

> 注：当有多个主轴时，仅返回第一主轴转速数据。

---

### 2.5.1.9 readLaserPower 读取激光功率

仅限激光切割机/焊接机。

```
GET  /api/cnc/readLaserPower?machineID=MACHINEID
```

**请求参数**

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| machineID | String | （必需）目标机台标识，详见 1.1.1 machineID 机台标识 |

**返回示例**

```json
{
  "preset": 100.0,
  "actual": 100.0
}
```

**返回参数**

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| preset | Float | 预设功率[%] |
| actual | Float | 实际功率[%] |

---

### 2.5.1.10 readLoad 读取负载数据

仅限 CNC。

```
GET  /api/cnc/readLoad?machineID=MACHINEID&channel=CHANNEL
```

**请求参数**

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| machineID | String | （必需）目标机台标识，详见 1.1.1 machineID 机台标识 |
| channel | Int32 | 机台通道号，如不补充则默认为 0，即主通道 |

**返回示例**

```json
{
  "spindleLoad": [
    100.0,
    0.0
  ],
  "axialLoad": [
    50.1,
    12.4,
    0.0
  ],
  "channel": 2
}
```

**返回参数**

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| spindleLoad | Float[] | 主轴负载[%] |
| axialLoad | Float[] | 伺服轴负载[%] |
| channel | Int32 | 机台通道号，仅当请求中补充 channel 且不为 0 时出现 |

> 注：当有多个主轴时，主轴负载中数据对应依次为第一主轴、第二主轴。伺服轴负载与 2.5.1.15 readPosition 读取坐标数据返回的 axisName 按顺序一一对应。

---

### 2.5.1.11 readLog 读取日志信息

目前支持 Fanuc 机器人、ABB 机器人、模拟机台。

```
GET  /api/cnc/readLog?machineID=MACHINEID
```

**请求参数**

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| machineID | String | （必需）目标机台标识，详见 1.1.1 machineID 机台标识 |
| type | String | 类型，范围：Alarm（警报日志）、Operation（操作日志）、Default（默认），默认为 Default。ABB 机器人支持 Operation；Fanuc 机器人支持 Alarm；模拟机台支持所有类型，Default 等同 Operation |
| count | Int32 | 日志数量，获取最新的指定数量的日志内容，默认为 0，即获取所有日志 |

**返回示例**（ABB 机器人 Operation）

```json
{
  "msgs": [
    "{\"SeqNo\":\"79\",\"Type\":\"1 \",\"Code\":\"10011\",\"Title\":\"电机上电(ON) 状态\",\"Date\":\"2025-04-02 T 10:54:40\",\"Description\":\"系统处于电机上电 (ON) 状态。\",\"Args\":[]}",
    "{\"SeqNo\":\"78\",\"Type\":\"1 \",\"Code\":\"10010\",\"Title\":\"电机下电 (OFF) 状态\",\"Date\":\"2025-04-02 T 10:54:39\",\"Description\":\"系统处于电机下电 (OFF) 状态。\",\"Args\":[]}"
  ]
}
```

**返回参数**

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| msgs | String[] | （必需）日志内容。注：不同机台的日志内容格式不同 |

---

### 2.5.1.12 readOffsetData 读取刀补数据

```
GET  /api/cnc/readOffsetData?machineID=MACHINEID&channel=CHANNEL&toolNum=TOOLNUM&offsetNum=OFFSETNUM
```

**请求参数**

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| machineID | String | （必需）目标机台标识，详见 1.1.1 machineID 机台标识 |
| channel | Int32 | 机台通道号，如不补充则默认为 0，即主通道 |
| toolNum | Int32 | 补充 toolNum 刀具号或 offsetNum 刀补号，以确定目标刀具。请求参数对应表格 26 刀补数据 `<tag>` 数据标签中的标签，参考表格 27 刀补数据标签组合中各系统的标签组合表，补充请求参数组合 |
| offsetNum | Int32 | 同上 |

**返回示例**（Heidenhain 海德汉）

```json
{
  "toolNum": 1,
  "toolName": "MILL_D2_ROUGH",
  "toolType": "9",
  "lengthWear": 0.6,
  "radiusWear": 0.5,
  "lengthGeom": 30.0,
  "radiusGeom": 1.0,
  "radius2Wear": 0.2,
  "radius2Geom": 2.0
}
```

**返回示例**（Siemens 西门子）

```json
{
  "toolNum": 2,
  "offsetNum": 1,
  "toolName": "ROUGHING_T80 A",
  "toolType": 500,
  "toolNose": 3,
  "wearX": 0.88,
  "wearZ": 0.78,
  "wearR": 0.05,
  "geomX": 55.0,
  "geomZ": 39.0,
  "geomR": 0.8
}
```

**返回参数**

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| channel | Int32 | 机台通道号，仅当请求中补充 channel 且不为 0 时出现 |
| toolNum | Int32 | 刀具号 |
| offsetNum | Int32 | 刀补号 |
| toolName | String | 刀具名称 |
| toolType | String | 刀具类型 |
| lengthUnit | String | 刀补长度单位 |
| toolNose | Int32 | （假想）刀尖位置/刀沿类型 |
| lengthWear | Double | 长度磨损 |
| radiuWear | Double | 半径磨损 |
| lengthGeom | Double | 长度形状 |
| radiusGeom | Double | 半径形状 |
| wearX | Double | 长度 X 磨损 |
| wearY | Double | 长度 Y 磨损 |
| wearZ | Double | 长度 Z 磨损 |
| wearR | Double | 半径磨损 |
| geomX | Double | 长度 X |
| geomY | Double | 长度 Y |
| geomZ | Double | 长度 Z |
| geomR | Double | 半径 |

> 注：表中为通用的刀补参数，但不是全部刀补参数。建议在第一次添加新类型机床时测试可读取的刀补参数。如需要获取机床中有但当前接口未添加的刀补设置参数，请咨询客服。

---

### 2.5.1.13 batchReadOffsetData 批量读取刀具补偿

此接口为 2.5.1.12 readOffsetData 读取刀补数据的批量化版本，通过在请求体中补充目标刀具列表，可以一次读取多组数据。

```
POST  /api/cnc/batchReadOffsetData?machineID=MACHINEID
```

**请求体示例** application/json：

```json
[
  {
    "toolNum": 1,
    "offsetNum": 1,
    "channel": 2
  },
  {
    "toolNum": 1,
    "offsetNum": 2,
    "channel": 2
  }
]
```

**请求参数**（与 2.5.1.12 readOffsetData 一致）

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| machineID | String | （必需）目标机台标识，详见 1.1.1 machineID 机台标识 |
| channel | Int32 | 机台通道号，如不补充则默认为 0，即主通道 |
| toolNum | Int32 | 补充 toolNum 刀具号或 offsetNum 刀补号，以确定目标刀具 |
| offsetNum | Int32 | 同上 |

**返回示例**

```json
[
  {
    "toolNum": 1,
    "offsetNum": 1,
    "lengthUnit": "MM",
    "toolNose": 3,
    "lengthWear": 0.0001,
    "radiusWear": 0.03,
    "lengthGeom": 5.0,
    "radiusGeom": 0.2,
    "channel": 2
  },
  {
    "toolNum": 1,
    "offsetNum": 2,
    "lengthUnit": "MM",
    "toolNose": 4,
    "lengthWear": 0.01,
    "radiusWear": 0.02,
    "lengthGeom": 6.0,
    "radiusGeom": 0.3,
    "channel": 2
  }
]
```

返回参数与 2.5.1.12 readOffsetData 读取刀补数据一致。如果某条请求失败，则返回数组中对应位置为该请求的错误信息。

---

### 2.5.1.14 writeOffsetData 写入刀补数据

```
POST  /api/cnc/writeOffsetData?machineID=MACHINEID&channel=CHANNEL&toolNum=TOOLNUM&offsetNum=OFFSETNUM
```

**请求体示例** application/json：

```json
{
  "toolNose": 3,
  "lengthWear": 0.0001,
  "radiusWear": 0.03,
  "lengthGeom": 5.0,
  "radiusGeom": 0.2
}
```

> 注：请求体中仅补充需要写入的参数。不改动的参数不用补充。

**请求参数**

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| machineID | String | （必需）目标机台标识，详见 1.1.1 machineID 机台标识 |
| channel | Int32 | 机台通道号，如不补充则默认为 0，即主通道 |
| toolNum | Int32 | 补充 toolNum 刀具号或 offsetNum 刀补号，以确定目标刀具 |
| offsetNum | Int32 | 同上 |
| toolName | String | 刀具名称（Siemens 西门子系统中为只读参数，不可写入） |
| toolType | String | 刀具类型（Okuma 大隈系统中为只读参数，不可写入） |
| lengthUnit | String | 刀补长度单位 |
| toolNose | Int32 | （假想）刀尖位置/刀沿类型 |
| lengthWear | Double | 长度磨损 |
| radiuWear | Double | 半径磨损 |
| lengthGeom | Double | 长度形状 |
| radiusGeom | Double | 半径形状 |
| wearX | Double | 长度 X 磨损 |
| wearY | Double | 长度 Y 磨损 |
| wearZ | Double | 长度 Z 磨损 |
| wearR | Double | 半径磨损 |
| geomX | Double | 长度 X |
| geomY | Double | 长度 Y |
| geomZ | Double | 长度 Z |
| geomR | Double | 半径 |

**返回示例**

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

**返回参数**

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| errorCode | Int32 | （必需）错误码，0 代表成功 |
| errorMsg | String | （必需）错误内容 |

---

### 2.5.1.15 readPosition 读取坐标数据

```
GET  /api/cnc/readPosition?machineID=MACHINEID&channel=CHANNEL
```

**请求参数**

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| machineID | String | （必需）目标机台标识，详见 1.1.1 machineID 机台标识 |
| channel | Int32 | 机台通道号，如不补充则默认为 0，即主通道 |

**返回示例**

```json
{
  "axisName": ["X", "Y", "Z"],
  "unit": ["mm", "mm", "mm"],
  "mach": [150.12, 0, -31.35],
  "abs": [150.12, 0, -31.35],
  "rel": [150.12, 0, -31.35],
  "dist": [9.88, 0, 31.35],
  "channel": 2
}
```

**返回参数**

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| axisName | String[] | （必需）轴名称 |
| unit | String[] | 单位 |
| mach | Float[] | 机械坐标 |
| abs | Float[] | 绝对坐标 |
| rel | Float[] | 相对坐标 |
| dist | Float[] | 剩余距离 |
| channel | Int32 | 机台通道号，仅当请求中补充 channel 且不为 0 时出现 |

> 注：各坐标数据与 axisName 中的坐标名按顺序对应。

---

### 2.5.1.16 readProgramBlock 读取程序段

此接口读取机台正在运行的程序段。

```
GET  /api/cnc/readProgramBlock?machineID=MACHINEID&channel=CHANNEL
```

**请求参数**

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| machineID | String | （必需）目标机台标识，详见 1.1.1 machineID 机台标识 |
| channel | Int32 | 机台通道号，如不补充则默认为 0，即主通道 |

**返回示例**

```json
{
  "currentBlock": "Y70.800",
  "channel": 2
}
```

**返回参数**

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| currentBlock | String | （必需）当前运行的程序段内容 |
| channel | Int32 | 机台通道号，仅当请求中补充 channel 且不为 0 时出现 |

---

### 2.5.1.17 readProgramInfo 读取加工程序信息

```
GET  /api/cnc/readProgramInfo?machineID=MACHINEID&channel=CHANNEL
```

**请求参数**

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| machineID | String | （必需）目标机台标识，详见 1.1.1 machineID 机台标识 |
| channel | Int32 | 机台通道号，如不补充则默认为 0，即主通道 |

**返回示例**

```json
{
  "mainPrgmName": "1234",
  "runningPrgName": "abcd",
  "programSeqNum": "N30",
  "programStack": "A/B/1234/abcd",
  "channel": 2
}
```

**返回参数**

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| mainPrgmName | String | （必需）主程序名 |
| runningPrgName | String | （必需）当前执行子程序名 |
| programSeqNum | String | 当前执行子程序段顺序号 |
| programStack | String | 程序堆栈，当前仅支持西门子和模拟机台 |
| channel | Int32 | 机台通道号，仅当请求中补充 channel 且不为 0 时出现 |

> 注：对于 Fanuc 发那科，标准程序名（字母 O+数字）中数字开头的零会自动去除，如在机床中显示程序名为 O0010，则返回程序名为 010。

---

### 2.5.1.18 readPlcData 读取 PLC 数据

使用此接口前，需要已知目标数据的 PLC 区域地址以及目标数据的类型。用户可以查找设备手册或联系设备厂家以获取相应信息。

```
GET  /api/cnc/readPlcData?machineID=MACHINEID&area=AREA&start=START&count=COUNT&type=TYPE
```

**请求参数**

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| machineID | String | （必需）目标机台标识，详见 1.1.1 machineID 机台标识 |
| area | String | （必需）区域。格式为 `{areaName}|{parameter1}|{paramter2}…`，其中 areaName 为区域名，parameter 是补充参数。areaName 为 PLC 的区域，比如 R、X、Y 等，可以从机床手册或厂家获取；如使用标准协议-MODBUS 通信，areaName 为 0x、1x、3x、4x 等分区名；网关支持一些特殊区域名，包括 $MACRO$宏变量，$PARAMS$系统参数，$TAG$标签等，参照表格 52 PLC 数据特殊区域与参数 |
| start | String | 起始地址。如非 10 进制，请参照表格 53 PLC 地址位进制前缀添加前缀 |
| count | Int32 | （必需）数据数量。对于非 String 类型，是目标数据的数量；对于 String 类型，是目标 String 的长度（以 Byte 计） |
| type | String | （必需）数据类型。目前支持的数据类型详见表格 54 PLC 数据类型 |
| channel | Int32 | 机台通道号，如不补充则默认为 0，即主通道 |

**表格 51 PLC 数据通用区域与参数**

| 设备系统 | 区域 | area 示例 | 备注 |
|---------|------|-----------|------|
| 全部设备系统 | 普通区域 | X | PLC 设备的 X 区域 |
| 全部设备系统 | 普通区域 | X\|s=S | 如与当前连接的 PLC 所连接的其它 PLC 通信，可补充 station 或 slot 或 salveID(MODBUS)；不补充 s 则默认为与当前连接的 PLC 通信。此参数目前仅部分 PLC 设备支持 |

**表格 52 PLC 数据特殊区域与参数（部分）**

| 系统型号 | 区域 | area 示例 | 备注 |
|---------|------|-----------|------|
| Bosunman 博尚 | MODBUS 地址 | 0x 或 1x 或 4x | |
| Brother 兄弟 | 宏变量 | $MACRO$ | |
| Delta 台达 | 宏变量 | $MACRO$ | |
| Fanuc 发那科 | 诊断数据 | $DIAG$ | |
| Fanuc 发那科 | 宏变量 | $MACRO$ | |
| Fanuc 发那科 | 系统参数 | $PARAMS$ | 支持 Byte、Int16、Int32 与 Double 等类型；部分参数需要补充轴号，如 axis=1 |
| Gsk 广州数控 [980, 988] | 宏变量 | $MACRO$ | |
| Haas 哈斯 [通用型] | 宏变量 | $MACRO$ | |
| Siemens 西门子 | 宏变量（R 参数） | $MACRO$\|type=R | |
| Siemens 西门子 | 宏变量（RG 参数） | $MACRO$\|type=RG | |
| Siemens 西门子 [通用型] | 标签（变量名） | $TAG$\|area=AREA\|block=BLOCK\|name=NAME\|areaNo=AREANO\|row=ROW\|column=COLUMN | S7 变量，补充参数 Area、Block(Component)、VariableName、AreaNo.(默认为1)、Row(默认为1)、Column(默认为1) |

**表格 53 PLC 地址位进制前缀**

| 进制 | 前缀（数字 0+字母） | 示例 |
|------|-------------------|------|
| 2 进制 | 0b | 0b1001 |
| 8 进制 | 0 | 03671 |
| 10 进制 | 无 | 123 |
| 16 进制 | 0x | 0x5A7 |

**表格 54 PLC 数据类型（部分系统支持情况）**

| 数据类型 | Bit | Byte | SByte | Int16 | UInt16 | Float | Int32 | UInt32 | Double | String |
|---------|-----|------|-------|-------|--------|-------|-------|--------|--------|--------|
| 博尚 | O | O | O | O | O | O | O | O | O | |
| 兄弟 | O | | | O | | | | | | |
| 台达 | O | O | O | O | O | O | O | O | O | O |
| 发那科 | | O | O | O | O | O | O | | * | |
| 西门子 | O | O | | O | O | O | O | O | O | O |
| 模拟机台 | O | O | O | O | O | O | O | O | O | O |

O: 支持；*: 仅支持宏变量（本地变量、公共变量等）使用该数据类型

**返回示例**（普通区域，Int32 数组）

```json
{
  "data": [2147483647, -616240881, 1351548766, 1408080714, -1677592641, 681965706, 1002992212, -1588442413]
}
```

**返回示例**（String 类型）

```json
{
  "msg": "Test string"
}
```

**返回参数**

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| data | Array[Object] | 请求参数中的 type 类型为除 String 以外的类型时，返回体中输出 data，类型为请求参数中指定的 type 类型数组，长度为请求参数中输入的数据数量 count |
| msg | String | 请求参数中的 type 类型为 String 时，返回体中输出 msg。msg 末尾的空位会自动截去 |
| channel | Int32 | 机台通道号，仅当请求中补充 channel 且不为 0 时出现 |

---

### 2.5.1.19 writePlcData 写入 PLC 数据

> **警告**：写入 PLC 数据有一定危险性，务必理解其风险，仅在安全情况下写入。默认设置下，网关屏蔽了该接口，使用前需要在网关管理页面**设置**页面打开对应选项。

使用此接口前，需要已知目标数据的 PLC 区域地址以及目标数据的类型。

```
POST  /api/cnc/writePlcData?machineID=MACHINEID&area=AREA&start=START&type=TYPE
```

**请求体示例**（写入非 String 类型）application/json：

```json
{
  "data": [2147483647, -616240881, 1351548766, 1408080714]
}
```

**请求体示例**（写入 String 类型）：

```json
{
  "msg": "Test string"
}
```

**请求参数**

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| machineID | String | （必需）目标机台标识，详见 1.1.1 machineID 机台标识 |
| area | String | （必需）区域，格式同 readPlcData 的 area 参数 |
| start | String | 起始地址。如非 10 进制，请参照表格 53 PLC 地址位进制前缀添加前缀 |
| type | String | （必需）数据类型，详见表格 54 PLC 数据类型 |
| data | Array[Object] | type 类型为非 String 时，请求体中输入 data，类型为请求参数中指定的 type 类型数组 |
| msg | String | type 类型为 String 时，请求体中输入 msg |
| channel | Int32 | 机台通道号，如不补充则默认为 0，即主通道 |

**返回示例**

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

**返回参数**

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| errorCode | Int32 | （必需）错误码，0 代表成功 |
| errorMsg | String | （必需）错误内容 |

---

### 2.5.1.20 readTimeData 读取机台时间数据

```
GET  /api/cnc/readTimeData?machineID=MACHINEID
```

**请求参数**

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| machineID | String | （必需）目标机台标识，详见 1.1.1 machineID 机台标识 |

**返回示例**

```json
{
  "cumulativePowerOnTimeMin": 34194,
  "cumulativePowerOnTimeMs": 0,
  "powerOnTimeMin": 2944,
  "powerOnTimeMs": 0,
  "cumulativeOperatingTimeMin": 6683,
  "cumulativeOperatingTimeMs": 13328,
  "operatingTimeMin": 0,
  "operatingTimeMs": 0,
  "cumulativeCuttingTimeMin": 0,
  "cumulativeCuttingTimeMs": 0,
  "cuttingTimeMin": 0,
  "cuttingTimeMs": 0,
  "lastCycleTimeMin": 4,
  "lastCycleTimeMs": 43000,
  "currentCycleTimeMin": 0,
  "currentCycleTimeMs": 0,
  "currentCuttingTimeMin": 0,
  "currentCuttingTimeMs": 0,
  "remainingCycleTimeMin": 0,
  "remainingCycleTimeMs": 0
}
```

**返回参数**

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| cumulativePowerOnTimeMin | Int32 | 累计通电时间 1[分钟] |
| cumulativePowerOnTimeMs | Int32 | 累计通电时间 2[毫秒] |
| powerOnTimeMin | Int32 | 当前通电时间 1[分钟] |
| powerOnTimeMs | Int32 | 当前通电时间 2[毫秒] |
| cumulativeOperatingTimeMin | Int32 | 累计运行时间 1[分钟] |
| cumulativeOperatingTimeMs | Int32 | 累计运行时间 2[毫秒] |
| operatingTimeMin | Int32 | 当前运行时间 1[分钟] |
| operatingTimeMs | Int32 | 当前运行时间 2[毫秒] |
| cumulativeCuttingTimeMin | Int32 | 累计加工（切削）时间 1[分钟] |
| cumulativeCuttingTimeMs | Int32 | 累计加工（切削）时间 2[毫秒] |
| cuttingTimeMin | Int32 | 当前加工（切削）时间 1[分钟] |
| cuttingTimeMs | Int32 | 当前加工（切削）时间 2[毫秒] |
| lastCycleTimeMin | Int32 | 上次循环时间 1[分钟] |
| lastCycleTimeMs | Int32 | 上次循环时间 2[毫秒] |
| currentCycleTimeMin | Int32 | 当前循环时间 1[分钟] |
| currentCycleTimeMs | Int32 | 当前循环时间 2[毫秒] |
| currentCuttingTimeMin | Int32 | 当前切削时间 1[分钟] |
| currentCuttingTimeMs | Int32 | 当前切削时间 2[毫秒] |
| remainingCycleTimeMin | Int32 | 剩余循环时间 1[分钟] |
| remainingCycleTimeMs | Int32 | 剩余循环时间 2[毫秒] |

> 注：接口仅返回机台系统支持的时间数据，各时间数据由对应的时间 1 和时间 2 换算相加得到，计算方法详见 1.2.24 TimeData：机台时间数据。

---

### 2.5.1.21 readToolLife 读取刀具寿命

```
GET  /api/cnc/readToolLife?machineID=MACHINEID&groupNum=GROUPNUM&toolIndex=TOOLINDEX&toolNum=TOOLNUM&offsetNum=OFFSETNUM
```

**请求参数**

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| machineID | String | （必需）目标机台标识，详见 1.1.1 machineID 机台标识 |
| groupNum | Int32 | 补充 groupNum 刀组号，或 toolIndex 组内序号，或 toolNum 刀具号，或 offsetNum 刀补号，以确定目标刀具 |
| toolIndex | Int32 | 同上 |
| toolNum | Int32 | 请求参数对应 1.2.25 ToolLife：刀具寿命数据中的标签，其中参数 groupNum 对应标签中的 toolGroupNum，参数 offsetNum 对应标签中的 toolOffsetNum，其他的参数与标签名称一致。参考 1.2.25 ToolLife：刀具寿命数据中各系统的标签组合表，补充请求参数组合 |
| offsetNum | Int32 | 同上 |

**返回示例**（Fanuc 发那科，计次）

```json
{
  "toolNum": 32,
  "toolIndex": 4,
  "toolGroupNum": 1,
  "countLimit": 100,
  "currentCount": 21
}
```

**返回示例**（Fanuc 发那科，计时）

```json
{
  "toolNum": 8,
  "toolIndex": 2,
  "toolGroupNum": 1,
  "timeLimit": 60000,
  "currentTime": 1380
}
```

**返回参数**

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| toolGroupNum | Int32 | 刀组号 |
| toolIndex | Int32 | 组内序号 |
| toolNum | Int32 | 刀具号 |
| toolOffsetNum | Int32 | 刀补号 |
| timeLimit | Int32 | 寿命极限[秒]，最大可用时间 |
| currentTime | Int32 | 当前寿命[秒]，已经使用的时间 |
| prewarningTime | Int32 | 预警寿命[秒]，当前寿命达到此值时发出警报 |
| countLimit | Int32 | 寿命极限[次]，最大可用次数 |
| currentCount | Int32 | 当前寿命[次]，已经使用的次数 |
| prewarningCount | Int32 | 预警寿命[次]，当前寿命达到此值时发出警报 |
| wearLimit | Int32 | 寿命极限[机床默认长度单位]，最大可磨损量 |
| currentWear | Int32 | 当前寿命[机床默认长度单位]，当前磨损量 |
| prewarningWear | Int32 | 预警寿命[机床默认长度单位]，当前寿命达到此值时发出警报 |

> 注：表中为通用的刀具寿命数据，为了所有机床系统标准统一，有些参数由原始数据换算得到。如需获取原始数据，可以使用 2.5.1.23 readToolLifeDetails 读取刀具寿命详情。各系统型号支持的寿命类型详见 1.2.25 ToolLife：刀具寿命数据。

---

### 2.5.1.22 batchReadToolLife 批量读取刀具寿命

此接口为 2.5.1.21 readToolLife 读取刀具寿命的批量化版本，通过在请求体中补充目标刀具列表，可以一次读取多组数据。

```
POST  /api/cnc/batchReadToolLife?machineID=MACHINEID
```

**请求体示例** application/json：

```json
[
  {
    "groupNum": 1,
    "toolIndex": 4
  },
  {
    "groupNum": 2,
    "toolIndex": 3
  }
]
```

**请求参数**（与 2.5.1.21 readToolLife 一致）

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| machineID | String | （必需）目标机台标识，详见 1.1.1 machineID 机台标识 |
| groupNum | Int32 | 补充 groupNum 刀组号，或 toolIndex 组内序号，或 toolNum 刀具号，或 offsetNum 刀补号，以确定目标刀具 |
| toolIndex | Int32 | 同上 |
| toolNum | Int32 | 同上 |
| offsetNum | Int32 | 同上 |

**返回示例**

```json
[
  {
    "toolNum": 32,
    "toolIndex": 4,
    "toolGroupNum": 1,
    "countLimit": 100,
    "currentCount": 21
  },
  {
    "toolNum": 23,
    "toolIndex": 3,
    "toolGroupNum": 2,
    "countLimit": 200,
    "currentCount": 58
  }
]
```

返回参数与 2.5.1.21 readToolLife 读取刀具寿命一致。如果某条请求失败，则返回数组中对应位置为该请求的错误信息。

---

### 2.5.1.23 readToolLifeDetails 读取刀具寿命详情

此接口除了读取到与 2.5.1.21 readToolLife 读取刀具寿命相同的通用刀具寿命数据，还能获取到未经处理的原始数据。一些不通用的刀具寿命数据会被添加到这个接口的返回参数中。

```
GET  /api/cnc/readToolLifeDetails?machineID=MACHINEID&groupNum=GROUPNUM&toolIndex=TOOLINDEX&toolNum=TOOLNUM&offsetNum=OFFSETNUM
```

**请求参数**（与 2.5.1.21 readToolLife 一致）

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| machineID | String | （必需）目标机台标识，详见 1.1.1 machineID 机台标识 |
| groupNum | Int32 | 补充 groupNum 刀组号，或 toolIndex 组内序号，或 toolNum 刀具号，或 offsetNum 刀补号，以确定目标刀具 |
| toolIndex | Int32 | 同上 |
| toolNum | Int32 | 同上 |
| offsetNum | Int32 | 同上 |

**返回示例**（Fanuc 发那科，计次）

```json
{
  "toolNum": 32,
  "toolIndex": 4,
  "toolGroupNum": 1,
  "countLimit": 100,
  "currentCount": 21,
  "rawToolLifeType": "Count",
  "rawToolLifeStatus": "Enabled"
}
```

**返回示例**（Fanuc 发那科，计时）

```json
{
  "toolNum": 8,
  "toolIndex": 2,
  "toolGroupNum": 1,
  "timeLimit": 60000,
  "currentTime": 1380,
  "rawToolLifeType": "Time",
  "rawToolLifeUnit": "Minute",
  "rawToolLifeStatus": "Enabled",
  "rawTimeLimit": 1000.0,
  "rawCurrentTime": 23.0
}
```

**返回示例**（Siemens 西门子，计时）

```json
{
  "toolNum": 1,
  "toolOffsetNum": 1,
  "timeLimit": 1800,
  "currentTime": 0,
  "prewarningTime": 1428,
  "rawToolLifeType": "Time",
  "rawToolLifeUnit": "Minute",
  "rawTimeLimit": 30.0,
  "rawRemainingTime": 30.0,
  "rawPrewarningRemainingTime": 6.2
}
```

**返回示例**（Siemens 西门子，计磨损）

```json
{
  "toolNum": 3,
  "toolOffsetNum": 1,
  "wearLimit": 2.0,
  "currentWear": 0.0,
  "prewarningWear": 1.0,
  "rawToolLifeType": "Wear",
  "rawRemainingWear": 2.0,
  "rawPrewarningRemainingWear": 1.0
}
```

**返回参数**

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| **通用数据** | | |
| groupNum | Int32 | 刀组号 |
| toolIndex | Int32 | 组内序号 |
| toolNum | Int32 | 刀具号 |
| toolOffsetNum | Int32 | 刀补号 |
| timeLimit | Int32 | 寿命极限[秒]，最大可用时间 |
| currentTime | Int32 | 当前寿命[秒]，已经使用的时间 |
| prewarningTime | Int32 | 预警寿命[秒]，当前寿命达到此值时发出警报 |
| countLimit | Int32 | 寿命极限[次]，最大可用次数 |
| currentCount | Int32 | 当前寿命[次]，已经使用的次数 |
| prewarningCount | Int32 | 预警寿命[次]，当前寿命达到此值时发出警报 |
| wearLimit | Int32 | 寿命极限[机床默认长度单位]，最大可磨损量 |
| currentWear | Int32 | 当前寿命[机床默认长度单位]，当前磨损量 |
| prewarningWear | Int32 | 预警寿命[机床默认长度单位]，当前寿命达到此值时发出警报 |
| **原始数据** | | |
| rawToolLifeType | String | 刀具寿命类型 |
| rawToolLifeUnit | String | 时间单位，以下简称为[时间] |
| rawToolLifeStatus | String | 刀具寿命状态 |
| rawTimeLimit | Double | 寿命极限[时间] |
| rawTimeLimit1 | Double | 刀具最长寿命[时间]，仅 Heidenhain 海德汉 |
| rawTimeLimit2 | Double | 调用刀具的最长寿命[时间]，仅 Heidenhain 海德汉 |
| rawCurrentTime | Double | 当前寿命[时间] |
| rawOverTime | Double | 超出刀具寿命[时间]，仅 Heidenhain 海德汉 |
| rawRemainingTime | Double | 剩余寿命[时间]，仅 Siemens 西门子 |
| rawPrewarningRemainingTime | Double | 预警剩余寿命[时间]，仅 Siemens 西门子 |
| rawRemainingCount | Int32 | 剩余寿命[次]，仅 Siemens 西门子 |
| rawPrewarningRemainingCount | Int32 | 预警剩余寿命[次]，仅 Siemens 西门子 |
| rawRemainingWear | Double | 剩余寿命[机床默认长度单位]，仅 Siemens 西门子 |
| rawPrewarningRemainingWear | Double | 预警剩余寿命[机床默认长度单位]，仅 Siemens 西门子 |
| inventoryNum | String | 刀具识别码，仅 Heidenhain 海德汉 |

> 注：表中通用数据部分即 2.5.1.21 readToolLife 读取刀具寿命中的返回参数。各系统型号支持的寿命类型详见 1.2.25 ToolLife：刀具寿命数据。

---

### 2.5.1.24 batchReadToolLifeDetails 批量读取刀具寿命详情

此接口为 2.5.1.23 readToolLifeDetails 读取刀具寿命详情的批量化版本，通过在请求体中补充目标刀具列表，可以一次读取多组数据。

```
POST  /api/cnc/batchReadToolLifeDetails?machineID=MACHINEID
```

**请求体示例** application/json：

```json
[
  {
    "groupNum": 1,
    "toolIndex": 4
  },
  {
    "groupNum": 2,
    "toolIndex": 3
  }
]
```

**请求参数**（与 2.5.1.23 readToolLifeDetails 一致）

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| machineID | String | （必需）目标机台标识，详见 1.1.1 machineID 机台标识 |
| groupNum | Int32 | 补充 groupNum 刀组号，或 toolIndex 组内序号，或 toolNum 刀具号，或 offsetNum 刀补号，以确定目标刀具 |
| toolIndex | Int32 | 同上 |
| toolNum | Int32 | 同上 |
| offsetNum | Int32 | 同上 |

**返回示例**

```json
[
  {
    "toolNum": 32,
    "toolIndex": 4,
    "toolGroupNum": 1,
    "countLimit": 100,
    "currentCount": 21
  },
  {
    "toolNum": 23,
    "toolIndex": 3,
    "toolGroupNum": 2,
    "countLimit": 200,
    "currentCount": 58
  }
]
```

返回参数与 2.5.1.23 readToolLifeDetails 读取刀具寿命详情一致。如果某条请求失败，则返回数组中对应位置为该请求的错误信息。

---

### 2.5.2 缓存读取

使用缓存读写类接口，网关会返回缓存中对应的自动采集任务数据。使用此类接口，必须启用网关的本地缓存功能（出厂设置默认开启），并且开启对应的自动采集任务。

---

### 2.5.2.1 readTaskData 读取机台任务数据

此接口从网关缓存中读取来自目标机台的任务数据，而不是向机台发出通讯请求读取数据，因此，通过此接口除了可以读取来自机台的原始数据（如机台状态、警报信息等），还能读取到经过网关统计处理的数据（过载时间、OEE 数据等），且运行效率更高，但前提是必须已开启目标数据对应的任务。

```
GET  /api/cnc/readTaskData?machineID=MACHINEID&type=TYPE&tag=TAG
```

**请求参数**

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| machineID | String | （必需）目标机台标识，详见 1.1.1 machineID 机台标识 |
| type | String | （必需）数据类型，详见 1.2 数据说明 |
| tag | String | 标签的字段形式，详见 1.2 数据说明 |

**示例**

获取 machineID 为 1010 的机台的 1 号主轴的过载数据，type=SpindleOverload，tag=S1，请求如下：

```
/api/cnc/readTaskData?machineID=1010&type=SpindleOverload&tag=S1
```

如果请求对应的机台任务未启用，或任务启用但是尚未执行时，返回结果为：

```json
{
  "errorCode": 1303,
  "errorMsg": "Task not ready."
}
```

工作正常时，以 JSON 格式返回指定数据类型的数据：

```json
{
  "cumulativeTime": 14367,
  "spindleNo": 1,
  "time": "2024-03-01T02:26:42.2781587Z"
}
```

> 注：返回参数中，time 是数据的获取时间。其它返回参数详见 1.2 数据说明中对应 type 包含的 field 与 tag 内容说明。

---

### 2.5.2.2 batchReadTaskData 批量读取机台任务数据

此接口为 2.5.2.1 readTaskData 读取机台任务数据的批量化版本，通过在请求体中补充目标数据列表，可以一次读取多组数据，前提是必须已开启目标数据对应的任务。

```
POST  /api/cnc/batchReadTaskData
```

**请求体示例** application/json：

```json
[
  {
    "machineID": "1",
    "type": "CNCStatus"
  },
  {
    "machineID": "2110",
    "type": "Count"
  },
  {
    "machineID": "3",
    "type": "PLC",
    "tag": "TR0"
  }
]
```

**请求参数**（与 2.5.2.1 readTaskData 一致）

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| machineID | String | （必需）目标机台标识，详见 1.1.1 machineID 机台标识 |
| Type | String | （必需）数据类型，详见 1.2 数据说明 |
| Tag | String | 标签的字段形式，详见 1.2 数据说明 |

**返回示例**

```json
[
  {
    "cncStatus": "AUTO_RUN",
    "alarmStatus": "ALARM",
    "alarmLevel": "WRN",
    "mode": "AUTO_RUN",
    "programStatus": "RUNNING",
    "emergencyStatus": "NOT_EMG",
    "dryRunStatus": "NOT_DRY_RUN",
    "adjustedStatus": "AUTO_RUN",
    "offTime": 2509,
    "waitTime": 2123,
    "emergencyTime": 25,
    "autoRunTime": 1526,
    "manualTime": 2905,
    "time": "2024-03-01T02:26:48.7383566Z"
  },
  {
    "count": 22406,
    "cumulativeCount": 0,
    "currentCount": 0,
    "time": "2024-03-01T02:26:49.8853233Z"
  },
  {
    "data": [32767],
    "tag": "R0",
    "time": "2024-03-01T02:26:49.3324829Z"
  }
]
```

返回结果顺序与请求顺序一致。如果某条请求失败，则返回数组中对应位置为该请求的错误信息。

> 注：返回参数中，time 是数据的获取时间。其它返回参数详见 1.2 数据说明中对应 type 包含的 field 与 tag 内容说明。

---

### 2.5.2.3 readGroupTaskData 读取机组任务数据

此接口从网关缓存中读取目标机组正在运行的目标任务的最新数据，前提是必须已开启目标机组的目标数据对应的任务。

```
GET  /api/cnc/readGroupTaskData?groupID=GROUPID&type=TYPE&tag=TAG
```

**请求参数**

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| groupID | String | （必需）目标机组标识，详见 1.1.2 groupID 机组标识 |
| type | String | （必需）数据类型，目前仅支持 1.2.11 GroupCount：机组加工计数数据、1.2.12 GroupCumulativeTime：机组累计状态时间，和 1.2.13 GroupOEE：机组 OEE |
| tag | String | 标签的字段形式，详见 1.2 数据说明 |

**示例**

获取 groupID 为 g2 的机组加工计数数据，type=GroupCount，请求如下：

```
/api/cnc/readGroupTaskData?groupID=g2&type=GroupCount
```

如果请求对应的机组任务未启用，或任务启用但尚未执行时，返回结果为：

```json
{
  "errorCode": 1303,
  "errorMsg": "Task not ready."
}
```

工作正常时，返回指定数据类型的数据：

```json
{
  "cumulativeCount": 127,
  "time": "2024-03-01T02:26:50.1127663Z"
}
```

---

### 2.5.2.4 batchReadGroupTaskData 批量读取机组任务数据

此接口为 2.5.2.3 readGroupTaskData 读取机组任务数据的批量化版本，通过在请求体中补充目标数据列表，可以一次读取多组数据，前提是必须已开启目标数据对应的任务。

```
POST  /api/cnc/batchReadTaskData
```

**请求体示例** application/json：

```json
[
  {
    "groupID": "1",
    "type": "GroupOEE"
  },
  {
    "groupID": "2",
    "type": "GroupCount"
  }
]
```

**请求参数**（与 2.5.2.1 readTaskData 一致）

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| groupID | String | 目标机组标识，详见 1.1.2 groupID 机组标识 |
| type | String | 数据类型，目前仅支持 1.2.11 GroupCount、1.2.12 GroupCumulativeTime 和 1.2.13 GroupOEE |
| tag | String | 标签的字段形式，详见 1.2 数据说明 |

**返回示例**

```json
[
  {
    "autoRunCount": 1,
    "waitCount": 0,
    "emergencyCount": 0,
    "manualCount": 0,
    "offCount": 0,
    "availability": 90.0,
    "offTime": 360,
    "waitTime": 0,
    "emergencyTime": 0,
    "autoRunTime": 3240,
    "manualTime": 0,
    "time": "2024-03-01T02:27:30.5534659Z"
  },
  {
    "cumulativeCount": 127,
    "time": "2024-03-01T02:27:30.6533778Z"
  }
]
```

返回结果顺序与请求顺序一致。如果某条请求失败，则返回数组中对应位置为该请求的错误信息。

---

### 2.5.2.5 batchReadErrors 批量读取机台连接状态

此接口用于批量获取指定机台当前连接状态，通过在请求体中补充目标机台标识，可以一次读取多台机台的连接状态。

```
POST  /api/cnc/batchReadErrors
```

**请求体示例** application/json：

```json
[
  {
    "machineID": "1"
  },
  {
    "machineID": "2"
  }
]
```

**请求参数**

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| machineID | String | （必需）目标机台标识，详见 1.1.1 machineID 机台标识 |

**返回示例**

```json
[
  {
    "errorCode": 0,
    "errorMsg": "Success"
  },
  {
    "errorCode": 3,
    "errorMsg": "Machine offline."
  }
]
```

返回结果顺序与请求顺序一致。

**返回参数**

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| errorCode | Int32 | （必需）错误码，0 代表连接成功 |
| errorMsg | String | （必需）错误内容 |

---

### 2.5.2.6 readErrorSummary 读取状态简报

此接口用于获取当前机台连接状态简报，无需请求参数。

```
GET  /api/cnc/readErrorSummary
```

**返回示例**

```json
{
  "success": 8,
  "offline": 1,
  "error": 0
}
```

**返回参数**

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| success | Int32 | （必需）连接成功机台数 |
| offline | Int32 | （必需）离线机台数 |
| error | Int32 | （必需）连接错误机台数 |
