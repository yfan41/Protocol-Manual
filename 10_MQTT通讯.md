## 四、MQTT 通讯

启用 MQTT 通讯，并开启目标机台/机组的自动采集任务（详见《说明书》5.3.1. 添加机台下的各类型机台的任务设置，以及 5.4.1.2. 机组任务设置）后，网关会基于 MQTT 协议，将自动采集任务结果数据转化成 JSON 格式的报文，并发布至指定 MQTT 服务器，数据上报的报文默认主题为"r"。网关也支持 RPC 接口。用户可以参照《说明书》5.6.3.MQTT 配置的说明配置 MQTT 服务。

---

### 4.1 数据上报报文格式

网关 MQTT 协议数据上报的报文支持多种模式，包括 Default, MKT, TB, TB2, Brm, IoTDA, WisIoT 等。

报文内容结构如下：

| 模式 | 数据上报报文格式 |
|------|----------------|
| Default | `{ "type": <type>, "unixtimestamp": <time>, "data": { "<field>": <value> }, "<tag>": <value>, "<entityID>": <value> }` |
| MKT | `{ "unixtimestamp": <time>, "<entityID>_<fieldID>_<field>": <value> }` |
| TB | `{ "<alias>": [ { "ts": <time>, "values": { "<fieldID>_<field>": <value> } } ] }` |
| TB2 | `{ "<machineID>": [ { "ts": <time>, "values": { "<fieldID>_<field>": <value> } } ] }` |
| Brm | `[ { "type": <type>, "ts": <time>, "data": { "<field>": <value> }, "<tag>": <value>, "<entityID>": <value> } ]` |
| IoTDA | `{ "devices": [ { "device_id": <entityID>, "services": [ { "service_id": <type>, "properties": { "<tagField>_<field>": <value> }, "event_time": <time> } ] } ] }` |
| WisIoT | `{ "properties": [ { "key": <entityID>_<fieldID>_<field>, "name": <fieldID>_<field>, "value": <value>, "time": <time> } ] }` |

#### 重要概念

1. **`<entityID>`** — 机台或机组标识：如当前数据属于机台数据时，`<entityID>` 为机台标识 machineID；如当前数据属于机组数据时，`<entityID>` 为机组标识 groupID。machineID 与 groupID 见 1.1 基本说明。

2. **`<alias>`** — 机台或机组名称：用户自定义的机台名称或机组名称（详见《说明书》5.3.1. 添加机台、5.4.1. 添加机组）。

3. **`<type>`** 类、**`<field>`** 字段与 **`<tag>`** 标签：一个**类**包含至少一个**字段**和不定数量的**标签**。**字段**是数据的名称。当某**类**不能仅以机台或机组进行区分时，我们需要为此数据种类添加**标签**。例如，类 SpindleOverload（主轴过载数据）包含**字段** CumulativeTime（累计过载时间），和**标签** SpindleNo（主轴号）。

4. **`<tagField>`** — 标签的字段形式，由**标签**缩写与**标签值**组成。如 S1 是 SpindleNo=1 的字段形式。

5. **`<fieldID>`** — 字段标识：若**类**中不包含**标签**，则**字段标识**与**类**一致。若**类**包含**标签**，则**字段标识**由**类**和**标签的字段形式**构成。**类**和多个**标签的字段形式**间由下划线隔开。**标签**在字段形式中的顺序由下文中**标签**的序号决定。例如，**字段标识** SpindleOverload_S1 表示第一主轴的过载数据，其中 S1 是 SpindleNo=1 的字段形式；**字段标识** ToolLife_G1_I2 表示刀组 1 中第 2 把刀的寿命数据，其中 G1_I2 是 GroupNum=1;Index=2 的字段形式。

6. **`<time>`** — 时间戳：表示任务执行时的时间，单位是毫秒。

7. **`<value>`** — 数值：数值或内容。

8. Default 和 MKT 模式，每条报文仅含来自同一机台或机组 `<entityID>` 的同一数据类 `<type>` 的数据。TB, TB2, IoTDA, Brm, WisIoT 等模式，支持把来自不同机台或机组 `<entityID>` 的不同数据类 `<type>` 的数据合并到一条报文中。

---

#### 报文示例

##### 示例 1（Default 模式）

```json
{
  "type": "CNCStatus",
  "unixtimestamp": 1698908463135,
  "data": {
    "cncStatus": "MANUAL",
    "alarmStatus": "NO_ALARM"
  },
  "machineID": "10"
}
```

- `"type": "CNCStatus"` — 数据类，用"CNCStatus"作为关键词，可以在 1.2 数据说明中找到 1.2.4.CNCStatus: 机台状态，识别本条报文中的数据为机台状态数据。
- `"unixtimestamp": 1698908463135` — 时间戳，即此条报文的时间戳。
- `"data": { "cncStatus": "MANUAL", "alarmStatus": "NO_ALARM" }` — 数据内容，可通过 `<type>` 申明的数据类查找对应内容的解释说明。
- `"machineID": "10"` — `<entityID>` 机台或机组标识，第 8 行内容代表这条报文的数据来自机台标识为 10 的机台。

##### 示例 2（Default 模式）

```json
{
  "type": "GroupCount",
  "unixtimestamp": 1698911434710,
  "data": {
    "cumulativeCount": 233
  },
  "groupID": 1
}
```

- `"type": "GroupCount"` — 数据类，用"GroupCount"作为关键词，可以在 1.2.11.GroupCount: 机组加工计数数据中找到，`"cumulativeCount"` 是数据 GroupCount 下的字段，代表机组累计加工计数。
- `"groupID": 1` — `<entityID>` 此处为 groupID 机组标识，代表这条报文的数据来自 1 号机组。

##### 示例 3（Default 模式）

```json
{
  "type": "ToolLife",
  "unixtimestamp": 1698908397751,
  "data": {
    "timeLimit": 1800,
    "currentTime": 1620,
    "prewarningTime": 1680
  },
  "toolNum": 9,
  "toolOffsetNum": 2,
  "machineID": "10"
}
```

- `"type": "ToolLife"` — 数据类，用"ToolLife"作为关键词，可以在 1.2.27.ToolLife: 刀具寿命数据中找到。
- `"data"` — `"timeLimit"` 是寿命极限[秒]，`"currentTime"` 是当前寿命[秒]，`"prewarningTime"` 是预警寿命[秒]。
- `"toolNum": 9, "toolOffsetNum": 2` — 这两行内容代表这条报文的刀补数据属于 9 号刀 2 号刀补。

##### 示例 4（MKT 模式）

```json
{
  "unixtimestamp": 1698908397751,
  "10_ToolLife_T9_02_timeLimit": 1800,
  "10_ToolLife_T9_02_currentTime": 1620,
  "10_ToolLife_T9_02_prewarningTime": 1680
}
```

- `"10_ToolLife_T9_02_timeLimit": 1800` — 格式为 `<entityID>_<fieldID>_<field>`: `<value>`
  - 10 是 `<entityID>` 机台标识
  - ToolLife_T9_02 是 `<fieldID>` 数据字段标识（ToolLife 是 `<type>`，T9_02 是标签字段形式，T 是 toolNum 缩写，O 是 toolOffsetNum 缩写）
  - timeLimit 是 `<field>` 字段（寿命极限[秒]）

##### 示例 5（TB 模式）

```json
{
  "机台 10": [
    {
      "ts": 1698908397751,
      "values": {
        "ToolLife_T9_02_timeLimit": 1800,
        "ToolLife_T9_02_currentTime": 1620,
        "ToolLife_T9_02_prewarningTime": 1680
      }
    }
  ]
}
```

- `"机台 10"` — `<alias>` 机台或机组名称，由用户在网关机台配置页添加/编辑机台（机组）窗口设置，如未设置，则默认 alias 与 entityID 机台或机组标识相同。
- `"values"` 中 `"ToolLife_T9_02_timeLimit": 1800` — 格式为 `<fieldID>_<field>`: `<value>`，ToolLife_T9_02 是 `<fieldID>`，timeLimit 是 `<field>`。

##### 示例 6（IoTDA 模式）

```json
{
  "devices": [
    {
      "device_id": "10",
      "services": [
        {
          "service_id": "ToolLife",
          "properties": {
            "T9_02_timeLimit": 1800,
            "T9_02_currentTime": 1620,
            "T9_02_prewarningTime": 1680
          },
          "event_time": 1698908397751
        }
      ]
    }
  ]
}
```

- `"device_id": "10"` — 格式为 `"device_id": <entityID>`，此处为 machineID 机台标识。
- `"service_id": "ToolLife"` — 格式为 `"service_id": <type>`，用"ToolLife"作为关键词，识别本条报文中的数据为刀具寿命数据。
- `"T9_02_timeLimit": 1800` — T9_02 是 `<tagField>` 标签的字段形式，T 是 toolNum 刀号的缩写，0 是 toolOffsetNum 刀补号的缩写；timeLimit 是 `<field>` 字段（寿命极限[秒]）。
- `"event_time": 1698908397751` — 格式为 `"event_time": <time>`，数值 1698908397751 即此条报文的时间戳。

---

### 4.2 RPC 接口

网关支持基于 MQTT 协议的 RPC 接口，用户必须在网关管理页面设定 RPC 请求主题和 RPC 回复主题，详见《说明书》5.6.3.MQTT 配置。设定完成后网关会监听 RPC 请求主题，并将 RPC 请求的结果发布至 RPC 回复主题。RPC 接口数据编码为 MQTT 配置中设定的编码。

如果需要在主题中嵌入 RPC 请求的标识，可在需要嵌入标识的位置使用通配符 `${request_id}` 表示。

例如：
- RPC 请求主题：`request/${request_id}`
- RPC 回复主题：`response/${request_id}`

按照此设置，当网关收到 `request/24` 的请求时，网关会将执行结果发布至主题 `response/24`。

> **注意：** `${request_id}` 的位置建议放在主题的尾部。如果需要把 `${request_id}` 位置放于主题中间，`${request_id}` 前必须有"/"作为分隔符。

网关 MQTT 协议支持多种报文模式，包括 Default, MKT, Brm, TB, TB2 等。

#### RPC 请求报文格式

| 模式 | RPC 请求报文格式 |
|------|----------------|
| Default/MKT/Brm | `{ "id": <request_id>, "method": "<method>", "params": <params> }` |
| TB/TB2 | `{ "device": "<deviceName>", "data": { "id": "<request_id>", "method": "<method>", "params": <params> } }` |
| WisIoT/IoTDA | 暂不支持 |

#### RPC 回复报文格式

| 模式 | RPC 回复报文格式 |
|------|----------------|
| Default/MKT/Brm | `{ "id": <request_id>, "data": <response> }` |
| TB/TB2 | `{ "device": "<deviceName>", "id": <request_id>, "data": <response> }` |
| WisIoT/IoTDA | 暂不支持 |

其中 `<response>` 为 RPC 命令的返回结果；`<deviceName>` 为机台名称；`<request_id>` 为自定义请求标识。注意在 TB 模式下，`<params>` 中的参数"machineID"无需输入，机台将由 `<deviceName>` 指定。

---

#### 支持的 RPC 命令

除了鉴权接口，和部分文件管理接口，RPC 命令与二、HTTP 通讯中对应的接口一一对应，输入参数与返回结果可参照二、HTTP 通讯中的接口说明。

| RPC 命令 | 说明 |
|----------|------|
| **数据读写接口—直接读写，前缀 /cnc/** | |
| readAlarm | 读取警报信息 |
| readCNCStatus | 读取机台状态 |
| readCNCStatusDetails | 读取机台状态详情 |
| readCount | 读取加工计数 |
| readCurrentToolNumber | 读取当前刀号 |
| readEnergyConsumption | 读取能耗数据 |
| readFeed | 读取进给数据 |
| readFeedAndSpindle | 读取进给转速数据 |
| readLaserPower | 读取激光功率 |
| readLoad | 读取负载数据 |
| readLog | 读取日志信息 |
| readOffsetData | 读取刀补数据 |
| batchReadOffsetData | 批量读取刀具补偿 |
| writeOffsetData | 写入刀补数据 |
| readPosition | 读取坐标数据 |
| readProgramBlock | 读取当前程序段 |
| readProgramInfo | 读取当前程序 |
| readPlcData | 读取 PLC 数据 |
| writePlcData | 写入 PLC 数据 |
| readTimeData | 读取机台时间数据 |
| readToolLife | 读取刀具寿命 |
| batchReadToolLife | 批量读取刀具寿命 |
| readToolLifeDetails | 读取刀具寿命详情 |
| batchReadToolLifeDetails | 批量读取刀具寿命详情 |
| **数据读写接口—缓存读取，前缀 /cnc/** | |
| readTaskData | 读取机台任务数据 |
| batchReadTaskData | 批量读取机台任务数据 |
| readGroupTaskData | 读取机组任务数据 |
| batchReadGroupTaskData | 批量读取机组任务数据 |
| batchReadErrors | 批量读取机台连接状态 |
| readErrorSummary | 读取状态简报 |
| **文件管理接口，前缀 /cnc/** | |
| readProgramList | 读取机台文件列表 |
| receiveFile | 接收机台文件 |
| readCurrentProgram | 接收机台当前运行的文件 |
| sendFile | 发送文件至机台 |
| batchSendFile | 批量发送文件至机台 |
| deleteFile | 删除机台文件 |
| lockFileByRange | 锁定/解锁机台程序编辑 |
| createDir | 创建机台目录 |
| deleteDir | 删除机台目录 |
| selectProgram | 选择程序 |
| readAllPrograms | 浏览所有文件 |
| searchFile | 搜索机台文件 |
| **机台数据分析接口，前缀 /analysis/** | |
| oee | 机台 OEE 数据分析 |
| alarm | 机台警报数据分析 |
| count | 机台计数数据分析 |
| overall | 机台综合数据分析 |
| cycle | 机台节拍数据分析 |
| **机组数据分析接口，前缀 /group-analysis/** | |
| oee | 机组 OEE 数据分析 |
| alarm | 机组警报数据分析 |
| count | 机组计数数据分析 |
| overall | 机组综合数据分析 |
| cycle | 机组节拍数据分析 |
| **历史数据接口，前缀 /db/** | |
| machine | 机台历史数据 |
| group-machine | 机组内所有机台历史数据 |
| query | 查询历史数据 |
| **全局配置接口，前缀 /config/** | |
| gateway-info | 获取网关信息 |
| settings | 获取网关全局设置 |
| update-settings | 修改网关全局设置 |
| security | 获取网关全局安全设置 |
| update-security | 修改网关全局安全设置 |
| **用户配置接口，前缀 /config/** | |
| user | 获取用户设置 |
| users | 获取所有用户设置 |
| create-user | 创建新用户 |
| update-user | 修改用户设置 |
| delete-user | 删除用户 |
| user-security | 获取用户安全设置 |
| update-user-security | 修改用户安全设置 |
| **机台配置接口，前缀 /config/** | |
| machine | 获取机台配置 |
| machines | 获取所有机台配置 |
| create-machine | 添加机台配置 |
| update-machine | 修改机台配置 |
| delete-machine | 删除机台配置 |
| batch-delete-machine | 批量删除机台配置 |
| **机组配置接口，前缀 /config/** | |
| group | 获取机组配置 |
| groups | 获取所有机组配置 |
| create-group | 添加机组配置 |
| update-group | 修改机组配置 |
| delete-group | 删除机组配置 |
| batch-delete-group | 批量删除机组配置 |
| **任务配置接口，前缀 /config/** | |
| machine-task-interval-settings | 获取机台任务间隔设置 |
| update-machine-task-interval-settings | 修改机台任务间隔设置 |
| group-task-interval-settings | 获取机组任务间隔设置 |
| update-group-task-interval-settings | 修改机组任务间隔设置 |
| oee-monitoring-settings | 获取 OEE 监控设置 |
| update-oee-monitoring-settings | 修改 OEE 监控设置 |
| tool-life-monitoring-settings | 获取刀具寿命监控设置 |
| update-oee-monitoring-settings | 修改刀具寿命监控设置 |
| count-monitoring-settings | 获取加工计数监控设置 |
| update-count-monitoring-settings | 修改加工计数监控设置 |
| overload-monitoring-settings | 获取过载监控设置 |
| update-overload-monitoring-settings | 修改过载监控设置 |
| alarm-monitoring-settings | 获取警报监控设置 |
| update-alarm-monitoring-settings | 修改警报监控设置 |
| machine-status-monitoring-settings | 获取机台状态监控设置 |
| update-machine-status-monitoring-settings | 修改机台状态监控设置 |
| **通讯配置接口，前缀 /config/** | |
| cloud-settings | 获取云平台设置 |
| update-cloud-settings | 修改云平台设置 |
| modbus-settings | 获取 MODBUS 设置 |
| update-modbus-settings | 修改 MODBUS 设置 |
| mqtt-settings | 获取 MQTT 设置 |
| update-mqtt-settings | 修改 MQTT 设置 |
| database-settings | 获取数据库设置 |
| update-database-settings | 修改数据库设置 |
| gateway-file-server-settings | 获取网关文件服务器设置 |
| update-gateway-file-server-settings | 修改网关服务器设置 |
| http-settings | 获取 HTTP 设置 |
| update-http-settings | 修改 HTTP 设置 |
| hub-settings | 获取 Hub 设置 |
| update-hub-settings | 修改 Hub 设置 |
| remote-access | 获取远程访问设置 |
| update-remote-access | 修改远程访问设置 |
| **Core 服务功能接口，前缀 /core/** | |
| info | 获取 Core 服务信息 |
| license-info | 获取许可信息 |
| service-status | 获取服务状态 |
| upload-license | 上传网关许可 |
| **Gateway 服务功能接口，前缀 /gateway/** | |
| alias | 获取网关名称 |
| update-alias | 修改网关名称 |
| reboot | 网关硬件重启 |
| restart | 重启所有服务 |
| restart-service | 重启 Core 服务 |
| shut-down | 网关关机 |
| internet-connection | 获取网关网络状态 |
| time | 获取网关当前时间 |
| sync-time | 同步网关时间 |
| time-zone | 获取网关时区 |
| time-zones | 获取时区选项 |
| update-time-zone | 修改网关时区 |
| network-adapters | 获取网关网络适配器列表 |
| lan | 获取有线网设置 |
| update-lan | 修改网关有线网设置 |
| wifi | 获取无线网设置 |
| update-wifi | 修改无线网设置 |
| search-wifi | 搜索无线网 |
| connect-wifi | 连接无线网 |
| disconnect-wifi | 断开无线网 |
| static-routing | 获取静态路由设置 |
| update-static-routing | 修改静态路由设置 |
| connect-remote-host | 连接远程服务器 |
| disconnect-remote-host | 断开远程服务器 |
| file-server-items | 获取网关文件服务器列表 |
| delete-file-server-item | 删除网关文件服务器项目 |

---

#### RPC 示例

##### 示例 1（Default 模式 — sendFile 发送文件）

在 Default 模式下，要发送 ASCII 编码的文件内容"fileContent"到 machineID 为 1010 的机台的 Dir/Prog 目录下，保存为 2222，应使用请求主题："request/1"，其中 1 为自定义请求标识。

请求报文：

```json
{
  "method": "sendFile",
  "params": {
    "machineID": "1010",
    "fileName": "2222",
    "dirAtCNC": "Dir/Prog",
    "data": {
      "content": "fileContent",
      "encoding": "us-ascii"
    }
  }
}
```

回复主题"response/1"，回复报文：

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

其中 machineID, fileName, dirAtCNC 为 HTTP 接口 2.6.6. sendFile 发送文件至机台 URL 中的请求参数，直接嵌入"params"字段里。POST 请求的请求体应嵌入"params"内的"data"字段中。

##### 示例 2（Default 模式 — writeOffsetData 写入刀补数据）

在 Default 模式下，要写入刀补号为 1 的刀补数据，应使用请求主题："request/2"，其中 2 为自定义请求标识。

请求报文：

```json
{
  "method": "writeOffsetData",
  "params": {
    "machineID": "1010",
    "offsetNum": 1,
    "data": {
      "toolNose": 3,
      "lengthWear": 0.0001,
      "radiusWear": 0.03,
      "lengthGeom": 0.0,
      "radiusGeom": 0.002
    }
  }
}
```

回复主题"response/2"，回复报文：

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

其中 machineID, offsetNum 为 HTTP 接口 2.5.1.14. writeOffsetData 写入刀补数据 URL 中的参数，直接嵌入"params"字段里。POST 请求的请求体应嵌入"params"内的"data"字段中。
