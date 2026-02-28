## 2.9 网关配置接口

网关配置接口用于命令网关执行特定功能，基地址 `/api/config`。

---

## 2.9.1 全局配置接口

### 2.9.1.1 gateway-info 获取网关信息

此接口无请求参数。

```
GET  /api/config/gateway-info
```

**返回示例**

```json
{
  "hid": "XXXXXX-XXXXXX-XXXXXX-XXXXXX",
  "uid": "XXXXXX-XXXXXX-XXXXXX-XXXXXX",
  "alias": "iotgw"
}
```

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| hid | String | （必需）网关硬件 ID |
| uid | String | （必需）网关 UID |
| alias | String | （必需）网关名称 |

---

### 2.9.1.2 settings 获取网关全局设置

此接口无请求参数。

```
GET  /api/config/settings
```

**返回示例**

```json
{
  "version": "1.19.3.102",
  "enableLocalCaching": true,
  "enableRemoteService": true,
  "enableLan2Setup": false,
  "timeServerAddress": "ntp1.aliyun.com;ntp2.aliyun.com;ntp3.aliyun.com;ntp4.aliyun.com;"
}
```

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| version | String | （必需）设置版本 |
| enableLocalCaching | Bool | （必需）启用本地缓存 |
| enableRemoteService | Bool | （必需）启用远程协助 |
| enableLan2Setup | Bool | （必需）启用 LAN2 设置 |
| timeServerAddress | String | （必需）时间服务器地址 |

---

### 2.9.1.3 update-settings 修改网关全局设置

```
POST  /api/gateway/update-settings
```

**请求体示例 application/json**

```json
{
  "enableLocalCaching": true,
  "enableRemoteService": true,
  "enableLan2Setup": false,
  "timeServerAddress": "ntp1.aliyun.com;ntp2.aliyun.com;ntp3.aliyun.com;ntp4.aliyun.com;"
}
```

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| enableLocalCaching | Bool | 启用本地缓存 |
| enableRemoteService | Bool | 启用远程协助 |
| enableLan2Setup | Bool | 启用 LAN2 设置 |
| timeServerAddress | String | 时间服务器地址 |

**返回示例**

```json
{
  "version": "1.19.3.102",
  "enableLocalCaching": true,
  "enableRemoteService": true,
  "enableLan2Setup": false,
  "timeServerAddress": "ntp1.aliyun.com;ntp2.aliyun.com;ntp3.aliyun.com;ntp4.aliyun.com;"
}
```

返回参数同请求参数，另附 `version`（设置版本）。

---

### 2.9.1.4 security 获取网关全局安全设置

此接口无请求参数。

```
GET  /api/config/security
```

**返回示例**

```json
{
  "enable": true,
  "protectedApi": "exact,/cnc/writeOffsetData;exact,/cnc/writePlcData"
}
```

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| enable | Bool | （必需）启用全局安全控制 |
| protectedApi | String | 受保护 API，即禁止任何用户使用的 API。未返回代表未设置。API 命令格式见下表。 |

**表格 58 API 命令格式**

| API 命令类型 | exact | prefix |
|-------------|-------|--------|
| 说明 | 指定具体一个 API 地址 | 指定有相同前缀的所有 API 地址 |
| 示例 | `exact,/cnc/writePlcData` | `prefix,/db` |

- API 地址从 "/api" 后开始。`"prefix,/"` 代表所有的 API 地址。
- 多条 API 地址命令以 ";" 分隔。

---

### 2.9.1.5 update-security 修改全局安全设置

```
POST  /api/gateway/update-security
```

**请求体示例 application/json**

```json
{
  "enable": true,
  "protectedApi": "exact,/cnc/writeOffsetData;exact,/cnc/writePlcData"
}
```

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| enable | Bool | （必需）启用全局安全控制 |
| protectedApi | String | 受保护 API。API 命令格式参考表格 58。 |

**返回示例**

```json
{
  "enable": true,
  "protectedApi": "exact,/cnc/writeOffsetData;exact,/cnc/writePlcData"
}
```

返回参数同请求参数。

---

## 2.9.2 用户配置接口

### 2.9.2.1 user 获取用户设置

此接口无请求参数。

```
GET  /api/config/user
```

**返回示例**

```json
{
  "id": 0,
  "username": "admin",
  "isAdmin": true
}
```

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| id | Int32 | （必需）数据库标识，详见 1.1.4 ID 数据库标识 |
| username | String | （必需）用户名 |
| isAdmin | Bool | （必需）是否有管理员权限 |

---

### 2.9.2.2 users 获取所有用户设置

此接口无请求参数。

```
GET  /api/config/users
```

**返回示例**

```json
[
  {
    "id": 0,
    "username": "admin",
    "isAdmin": true
  },
  {
    "id": 1,
    "username": "user1",
    "isAdmin": false
  }
]
```

返回参数同 2.9.2.1。

---

### 2.9.2.3 create-user 创建新用户

新用户的初始密码与用户名相同。

```
POST  /api/config/create-user
```

**请求体示例 application/json**

```json
{
  "username": "user2",
  "isAdmin": false
}
```

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| username | String | （必需）用户名 |
| isAdmin | Bool | 是否有管理员权限，默认为 true |

**返回示例**

```json
{
  "id": 2,
  "username": "user2",
  "isAdmin": false
}
```

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| id | Int32 | （必需）数据库标识 |
| username | String | （必需）用户名 |
| isAdmin | Bool | （必需）是否有管理员权限 |

---

### 2.9.2.4 update-user 修改用户设置

修改用户名时，密码不会被修改。

```
POST  /api/config/update-user
```

**请求体示例 application/json**

```json
{
  "id": 2,
  "username": "user2",
  "isAdmin": false
}
```

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| id | Int32 | （必需）数据库标识 |
| username | String | 用户名，默认不修改 |
| isAdmin | Bool | 是否有管理员权限，默认不修改 |

**返回示例**

```json
{
  "id": 2,
  "username": "user2",
  "isAdmin": false
}
```

返回参数同 2.9.2.3。

---

### 2.9.2.5 delete-user 删除用户

```
GET  /api/config/delete-user?id=ID
```

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| id | Int32 | （必需）数据库标识 |

**返回示例**

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

---

### 2.9.2.6 user-security 获取用户安全设置

```
GET  /api/config/user-security?id=ID
```

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| id | Int32 | （必需）数据库标识 |

**返回示例**

```json
{
  "id": 2,
  "secretKey": "sk-XXXXXXXXXX",
  "authorizedApis": "prefix,/cnc;prefix,/db;prefix,/analysis;prefix,/group-analysis;prefix,/config;prefix,/core;prefix,/gateway;prefix,/auth;"
}
```

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| id | Int32 | （必需）数据库标识 |
| secretKey | String | 密钥，以 "sk-" 前缀开头。未返回代表未设置。 |
| authorizedApis | String | 授权 API，即用户可以使用的 API。未返回代表未设置。API 命令格式参考表格 58。 |

---

### 2.9.2.7 update-user-security 修改用户安全设置

```
POST  /api/config/update-user-security
```

**请求体示例 application/json**

```json
{
  "id": 2,
  "secretKey": "Token",
  "authorizedApis": "prefix,/cnc;prefix,/db;prefix,/analysis;prefix,/group-analysis;prefix,/config;prefix,/core;prefix,/gateway;prefix,/auth;"
}
```

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| id | Int32 | （必需）数据库标识 |
| secretKey | String | 密钥，以 "sk-" 前缀开头 |
| authorizedApis | String | 授权 API。API 命令格式参考表格 58。 |

**返回示例**

```json
{
  "id": 2,
  "secretKey": "Token",
  "authorizedApis": "prefix,/cnc;prefix,/db;prefix,/analysis;prefix,/group-analysis;prefix,/config;prefix,/core;prefix,/gateway;prefix,/auth;"
}
```

返回参数同请求参数。

---

## 2.9.3 机台配置接口

机台配置接口用于获取、新增、修改，或删除机台配置。机台配置相关说明可以参考《说明书》5.3 机台配置。

**表格 59 机台配置参数**

| 参数 | 类型 | 说明 |
|------|------|------|
| id | Int32 | 数据库标识 |
| machineType | String | 机台类型，详见表格 60 machineType 对应机台类型 |
| system | String | 系统 |
| model | String | 型号 |
| name | String | 机台名 |
| machineID | String | 机台标识 |
| slaveID | Int32 | 从站标识 |
| ip | String | IP 地址 |
| port | Int32 | 端口号，0 代表各设备的默认端口号 |
| isActive | Bool | 激活状态，true=激活，false=未激活 |
| useDefaultMachineID | Bool | 使用默认机台标识 |
| useDefaultSlaveID | Bool | 使用默认从站标识 |
| customAttributes | String | 自定义属性，示例：`{"attribute": value,...}` |
| username | String | 用户名，部分设备需要 |
| permission | String | 权限，仅 Delta 台达 CNC 系统需要设置，范围：NORMAL, USER_1, USER_2, MACHIN, SYSTEM，默认为 NORMAL |
| password | String | 密码，部分设备需要 |
| version | String | 版本，部分设备需要 |
| connectionMode | String | 连接方式，部分设备需要 |
| encoding | String | 编码，如 ASCII, UTF-8, GBK 等。默认为 default 自动获取 |
| language | String | 警报语言。仅 Siemens 西门子系统需要设置，目前支持 English, Chinese，默认为 Chinese |
| numberOfTasks | Int32 | 启用的任务数。只读 |
| taskAlarm | Bool | 警报信息任务，true=开启，false=关闭 |
| taskAlarmPrecond | String | 警报信息任务前置条件，未返回代表未设置 |
| taskAlarmEnableCustomAlarm | Bool | 自定义警报，true=开启，false=关闭。开启后以自定义警报代替默认警报 |
| taskAlarmCustomAlarmStatusTag | String | 自定义警报 alarmStatus 标签，填入对应 PLC 数据任务结果标签 |
| taskAlarmCustomAlarmMsgTag | String | 自定义警报 alarmMsg 标签，填入对应 PLC 数据任务结果标签 |
| taskMachineStatus | Bool | 机台状态任务，true=开启，false=关闭 |
| taskChannelMachineStatus | String | 机台状态任务目标通道，如 "0;1-3" |
| taskMachineStatusPrecond | String | 机台状态任务前置条件，未返回代表未设置 |
| taskMachineStatusEnableAdjustedManual | Bool | 机台状态任务调机状态修正，true=开启，false=关闭 |
| taskMachineStatusMaxManualPauseTime | Int32 | 机台状态任务最长调机暂停时间（秒） |
| taskMachineStatusEnableStatusConversion | Bool | 机台状态任务状态转换，true=开启，false=关闭 |
| taskMachineStatusStatusConversionSettings | String | 机台状态任务满足条件时转换状态 |
| taskMachineStatusEnableCustomStatus | Bool | 自定义机台状态任务，true=开启，false=关闭。开启后以自定义状态代替默认状态 |
| taskMachineStatusCustomStatusCNCStatusTag | String | 自定义状态 cncStatus 标签，填入对应 PLC 数据任务结果标签 |
| taskMachineStatusCustomStatusAlarmStatusTag | String | 自定义状态 alarmStatus 标签，填入对应 PLC 数据任务结果标签 |
| taskCount | Bool | 加工计数任务，true=开启，false=关闭 |
| taskCountPrecond | String | 加工计数任务前置条件，未返回代表未设置 |
| taskCountEnableCustomCount | Bool | 自定义加工计数任务，true=开启，false=关闭 |
| taskCountCustomCountTag | String | 自定义加工计数标签，填入对应 PLC 数据任务结果标签 |
| taskCurrentToolNo | Bool | 当前刀号任务，true=开启，false=关闭 |
| taskChannelCurrentToolNo | String | 当前刀号任务目标通道，如 "0;1-3" |
| taskCurrentToolNoPrecond | String | 当前刀号任务前置条件，未返回代表未设置 |
| taskEnergyConsumption | Bool | 能耗任务，true=开启，false=关闭 |
| taskEnergyConsumptionPrecond | String | 能耗任务前置条件，未返回代表未设置 |
| taskFeed | Bool | 进给任务，true=开启，false=关闭 |
| taskChannelFeed | String | 进给任务目标通道，如 "0;1-3" |
| taskFeedPrecond | String | 进给任务前置条件，未返回代表未设置 |
| taskFeedAndSpindle | Bool | 进给转速任务，true=开启，false=关闭 |
| taskChannelFeedAndSpindle | String | 进给转速任务目标通道，如 "0;1-3" |
| taskFeedAndSpindlePrecond | String | 进给转速任务前置条件，未返回代表未设置 |
| taskLaserPower | Bool | 激光功率任务，true=开启，false=关闭 |
| taskLoad | Bool | 负载数据任务，true=开启，false=关闭 |
| taskChannelLoad | String | 负载数据任务目标通道，如 "0;1-3" |
| taskLoadPrecond | String | 负载数据任务前置条件，未返回代表未设置 |
| taskLogHistory | Bool | 日志历史任务，true=开启，false=关闭 |
| taskLogHistoryPrecond | String | 日志历史任务前置条件，未返回代表未设置 |
| taskOverLoad | Bool | 过载监控任务，true=开启，false=关闭 |
| taskChannelOverLoad | String | 过载监控任务目标通道，如 "0;1-3" |
| taskOverLoadPrecond | String | 过载监控任务前置条件，未返回代表未设置 |
| taskPlcData | Bool | PLC 数据任务，true=开启，false=关闭 |
| taskPlcDataSettings | String | PLC 数据任务设置 |
| taskPosition | Bool | 坐标数据任务，true=开启，false=关闭 |
| taskChannelPosition | String | 坐标数据任务目标通道，如 "0;1-3" |
| taskPositionPrecond | String | 坐标数据任务前置条件，未返回代表未设置 |
| taskCurrentProgramBlock | Bool | 当前程序段任务，true=开启，false=关闭 |
| taskCurrentProgramBlockPrecond | String | 当前程序段任务前置条件，未返回代表未设置 |
| taskProgramInfo | Bool | 加工程序任务，true=开启，false=关闭 |
| taskChannelProgramInfo | String | 加工程序任务目标通道，如 "0;1-3" |
| taskProgramInfoPrecond | String | 加工程序任务前置条件，未返回代表未设置 |
| taskMachineTimeData | Bool | 机台时间任务，true=开启，false=关闭 |
| taskMachineTimeDataPrecond | String | 机台时间任务前置条件，未返回代表未设置 |
| taskToolLife | Bool | 刀具寿命任务，true=开启，false=关闭 |
| taskToolLifePrecond | String | 刀具寿命任务前置条件，未返回代表未设置 |
| taskToolLifeGroupNumIndex | String | 刀具寿命任务目标刀具，{刀组号.组内序号} 格式 |
| taskToolLifeIndex | String | 刀具寿命任务目标刀具，{序号} 格式 |
| taskToolLifeToolNum | String | 刀具寿命任务目标刀具，{刀具号} 格式 |
| taskToolLifeToolNumOffsetNum | String | 刀具寿命任务目标刀具，{刀具号.刀补号} 格式 |
| taskToolOffset | Bool | 刀具补偿任务，true=开启，false=关闭 |
| taskChannelToolOffset | String | 刀具补偿任务目标通道，如 "0;1-3" |
| taskToolOffsetPrecond | String | 刀具补偿任务前置条件，未返回代表未设置 |
| taskToolOffsetToolNum | String | 刀具补偿任务目标刀具，{刀具号} 格式 |
| taskToolOffsetOffsetNum | String | 刀具补偿任务目标刀具，{刀补号} 格式 |
| taskToolOffsetToolNumOffsetNum | String | 刀具补偿任务目标刀具，{刀具号.刀补号} 格式 |
| taskAlarmHistory | Bool | 警报历史任务，true=开启，false=关闭 |
| taskAlarmHistoryPrecond | String | 警报历史任务前置条件，未返回代表未设置 |
| taskCycleData | Bool | 节拍数据任务，true=开启，false=关闭 |
| taskCycleDataPrecond | String | 节拍数据任务前置条件，未返回代表未设置 |
| taskOEE | Bool | OEE 监控任务，true=开启，false=关闭 |
| taskOEEPrecond | String | OEE 监控任务前置条件，未返回代表未设置 |
| taskExternalPlcDataSettings | String | 外部 PLC 任务设置 |
| parallelProcessing | Int | 并行任务处理数 |
| fileServerType | String | 文件服务器类型（程序传输），可能的值有 Machine Memory, Shared Folder, Shared Folder (Win XP), FTP Server, Wireless Disk, Gateway File Server |
| fileServerAddress | String | 服务器地址（程序传输） |
| fileServerPort | Int32 | 自定义端口（程序传输） |
| fileServerUsername | String | 用户名（程序传输） |
| fileServerPassword | String | 密码（程序传输） |
| fileServerUCode | String | U 码（程序传输） |
| fileServerRootDir | String | 根目录（程序传输） |
| modbusSlaveID | Int32 | 从站 ID（常规），MODBUS 通讯需要，默认为 1 |
| modbusByteOrder4 | String | 32 位型格式，MODBUS 通讯需要，范围：DCBA, BADC, ABCD, CDAB |
| modbusByteOrder8 | String | 64 位型格式，MODBUS 通讯需要，范围：HGFEDCBA, BADCFEHG, ABCDEFGH, GHEFCDAB |
| modbusPLCAddress | Bool | 使用 PLC 地址，MODBUS 通讯需要，true=开启，false=关闭。启用后目标地址位偏移 +1 |
| modbusReverseString | Bool | 反转字符串，MODBUS 通讯需要，true=开启，false=关闭 |

> **注意：** 接口仅返回已设置项。password 参数无论是否已设置都不会返回。机台的 machineType 参数不同，支持的任务不同，接口返回支持任务的相关参数。

**表格 60 machineType 对应机台类型**

| machineType | 机台类型 |
|-------------|---------|
| CNC | CNC |
| LASER | 激光切割机 |
| PLC | PLC |
| ROBOT | 机器人 |

---

### 2.9.3.1 machine 获取机台配置

```
GET  /api/config/machine?ID=ID&machineID=MACHINEID
```

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| id | Int32 | 数据库标识 |
| machineID | String | 目标机台标识。如 ID 与 machineID 同时输入，machineID 被忽略 |

**返回示例**

```json
{
  "id": 1,
  "machineType": "CNC",
  "system": "Mock",
  "model": "General",
  "name": "演示 1",
  "useDefaultMachineID": true,
  "machineID": "1",
  "useDefaultSlaveID": true,
  "slaveID": 1,
  "customAttributes": "",
  "encoding": "Default",
  "ip": "127.0.0.1",
  "port": 0,
  "isActive": true,
  "numberOfTasks": 20,
  "taskAlarm": true,
  "taskMachineStatus": true,
  "taskChannelMachineStatus": "0",
  "taskCount": true,
  "taskCurrentToolNo": true,
  "taskEnergyConsumption": true,
  "taskFeedAndSpindle": true,
  "taskChannelFeedAndSpindle": "0",
  "taskLoad": true,
  "taskChannelLoad": "0",
  "taskLogHistory": true,
  "taskOverLoad": true,
  "taskChannelOverLoad": "0",
  "taskPlcData": true,
  "taskPlcDataSettings": "R,0,10,Int16;Y,0,3,Bit0",
  "taskPosition": true,
  "taskChannelPosition": "0",
  "taskCurrentProgramBlock": true,
  "taskChannelCurrentProgramBlock": "0",
  "taskProgramInfo": true,
  "taskChannelProgramInfo": "0",
  "taskMachineTimeData": true,
  "taskToolLife": true,
  "taskToolOffset": true,
  "taskChannelToolOffset": "0",
  "taskToolOffsetToolNumOffsetNum": "1.2;3-5.1;7-9.1-3",
  "taskAlarmHistory": true,
  "taskCycleData": true,
  "taskOEE": true,
  "parallelProcessing": 1,
  "fileServerType": "Machine Memory",
  "fileServerRootDir": ""
}
```

返回参数见表格 59 机台配置参数。

---

### 2.9.3.2 machines 获取所有机台配置

此接口无请求参数。

```
GET  /api/config/machines
```

返回所有机台配置的数组。返回参数见表格 59 机台配置参数。

---

### 2.9.3.3 create-machine 添加机台配置

```
POST  /api/config/create-machine
```

**请求体示例**

```json
{
  "machineType": "PLC",
  "system": "Standard Protocols",
  "model": "Modbus TCP",
  "name": "150",
  "useDefaultMachineID": false,
  "machineID": "M150",
  "useDefaultSlaveID": true,
  "ip": "127.0.0.150",
  "isActive": true,
  "taskPlcData": true,
  "taskPlcDataSettings": "4x,100,2,Int32;0x,100,10,Bit0;"
}
```

请求参数见表格 59 机台配置参数。注意请求体中不用设置数据库标识 id，id 由网关自动分配，创建成功后出现在返回体中。

返回参数见表格 59 机台配置参数。

---

### 2.9.3.4 update-machine 修改机台配置

修改指定机台的配置信息并返回修改后的机台配置信息。

```
POST  /api/config/update-machine
```

**请求体示例**

```json
{
  "id": 21,
  "name": "newName",
  "useDefaultMachineID": false,
  "machineID": "newMachineID",
  "useDefaultSlaveID": false,
  "slaveID": 192,
  "ip": "127.0.0.192",
  "port": 6000,
  "isActive": false,
  "taskPlcData": false
}
```

请求参数见表格 59 机台配置参数。注意：此接口使用 id 作为唯一标识，请求体中必须设置数据库标识 id。机台标识 machineID 可以通过这个接口修改。

返回参数见表格 59 机台配置参数。

---

### 2.9.3.5 delete-machine 删除机台配置

```
GET  /api/config/delete-machine?ID=ID&machineID=MACHINEID
```

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| id | Int32 | 数据库标识 |
| machineID | String | 目标机台标识。如 ID 与 machineID 同时输入，machineID 被忽略 |

**返回示例**

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

---

### 2.9.3.6 batch-delete-machines 批量删除机台配置

此接口为 2.9.3.5 delete-machine 删除机台配置的批量版本，通过在请求体中补充数据库 ID，可以一次删除多个机台。

```
POST  /api/config/batch-delete-machines
```

**请求体示例 application/json**

```json
{
  "ids": [2, 3, 4]
}
```

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| ids | Int32[] | （必需）数据库标识数组 |

**返回示例**

```json
{
  "deleted": 3
}
```

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| deleted | Int32 | （必需）删除机台数 |

---

## 2.9.4 机组配置接口

机组配置接口用于获取、新增、修改，或删除机组配置。机组配置相关说明可以参考《说明书》5.4 机组配置。

**表格 61 机组配置参数**

| 参数 | 类型 | 说明 |
|------|------|------|
| id | Int32 | 机组数据库标识 |
| number | Int32 | 机台，有效范围 1~8 |
| useDefaultGroupID | Bool | 使用默认机组标识 |
| groupID | String | 机组标识 |
| name | String | 机台名 |
| isActive | Bool | 激活状态，true=激活，false=未激活 |
| machines | Object[] | 机组包含的机台信息，详见表格 62 machines 机台列表信息 |
| enableExternalMachines | Bool | 启用外部机台 |
| externalMachines | String | 外部机台命令 |
| taskCount | Bool | 机组加工计数任务，true=开启，false=关闭 |
| taskOEE | Bool | 机组 OEE 监控任务，true=开启，false=关闭 |
| taskCumulativeStatusTime | Bool | 机组累计状态时间任务，true=开启，false=关闭 |

**表格 62 machines 机台列表信息**

| 参数 | 类型 | 说明 |
|------|------|------|
| id | Int32 | 机台数据库标识。在列表中添加或移除 id 以添加指定机台到机组或从机组移除 |
| machineType | String | 机台类型 |
| system | String | 系统 |
| model | String | 型号 |
| name | String | 机台名 |
| machineID | String | 机台标识。在列表中添加或移除 machineID 以添加指定机台到机组或从机组移除。如 ID 与 machineID 同时输入，machineID 被忽略 |
| slaveID | Int32 | 从站标识 |
| ip | String | IP 地址 |
| port | Int32 | 端口号，0 代表各设备的默认端口号 |
| isActive | Bool | 激活状态 |
| countMultiplier | Int32 | 产量系数 |

---

### 2.9.4.1 group 获取机组配置

```
GET  /api/config/group?ID=ID&groupID=GROUPID
```

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| id | Int32 | 数据库标识 |
| groupID | String | 目标机组标识。如 ID 与 groupID 同时输入，groupID 被忽略 |

**返回示例**

```json
{
  "id": 1,
  "number": 1,
  "useDefaultGroupID": true,
  "groupID": "g1",
  "name": "g1",
  "isActive": true,
  "machines": [
    {
      "id": 4,
      "machineType": "CNC",
      "system": "Mock",
      "model": "General",
      "name": "演示 1",
      "machineID": "1",
      "slaveID": 1,
      "ip": "127.0.0.1",
      "port": 0,
      "isActive": true,
      "countMultiplier": 1
    }
  ],
  "enableExternalMachines": false,
  "taskCount": true,
  "taskOEE": true,
  "taskCumulativeStatusTime": true
}
```

返回参数见表格 61 机组配置参数。

---

### 2.9.4.2 groups 获取所有机组配置

此接口无请求参数。

```
GET  /api/config/groups
```

返回所有机组配置的数组。返回参数见表格 61 机组配置参数。

---

### 2.9.4.3 create-group 添加机组配置

```
POST  /api/config/create-group
```

**请求体示例**

```json
{
  "number": 7,
  "name": "g7",
  "useDefaultGroupID": false,
  "groupID": "group7",
  "isActive": true,
  "machines": [
    {"id": 6, "countMultiplier": 23},
    {"id": 7, "machineID": "4", "countMultiplier": 4}
  ],
  "enableExternalMachines": false,
  "taskCount": true,
  "taskOEE": true,
  "taskCumulativeStatusTime": true
}
```

请求参数见表格 61 机组配置参数。注意请求体中不用设置数据库标识 id，id 由网关自动分配，创建成功后出现在返回体中。

返回参数见表格 61 机组配置参数。

---

### 2.9.4.4 update-group 修改机组配置

修改指定机组的配置信息并返回修改后的机组配置信息。

```
POST  /api/config/update-group
```

**请求体示例**

```json
{
  "id": 3,
  "number": 6,
  "name": "newName",
  "useDefaultGroupID": false,
  "groupID": "newGroupID",
  "isActive": true,
  "machines": [{"id": 6, "countMultiplier": 1}],
  "taskCount": false,
  "taskOEE": false,
  "taskCumulativeStatusTime": false
}
```

请求参数见表格 61 机组配置参数。注意：此接口使用 id 作为唯一标识，请求体中必须设置数据库标识 id。机组标识 groupID 可以通过这个接口修改。

返回参数见表格 61 机组配置参数。

---

### 2.9.4.5 delete-group 删除机组配置

```
GET  /api/config/delete-group?ID=ID&groupID=GROUPID
```

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| ID | Int32 | 数据库标识 |
| groupID | String | 目标机组标识。如 ID 与 groupID 同时输入，groupID 被忽略 |

**返回示例**

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

---

### 2.9.4.6 batch-delete-groups 批量删除机组配置

此接口为 2.9.4.5 delete-group 删除机组配置的批量版本，通过在请求体中补充数据库 ID，可以一次删除多个机组。

```
POST  /api/config/batch-delete-groups
```

**请求体示例 application/json**

```json
{
  "ids": [2, 3, 4]
}
```

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| ids | Int32[] | （必需）数据库标识数组 |

**返回示例**

```json
{
  "deleted": 3
}
```

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| deleted | Int32 | （必需）删除机组数 |

---

## 2.9.5 任务配置接口

任务配置接口用于获取或修改任务配置。任务配置相关说明可以参考《说明书》5.5 任务配置。

### 2.9.5.1 machine-task-interval-settings 获取机台任务间隔设置

此接口无请求参数。

```
GET  /api/config/machine-task-interval-settings
```

**返回示例**

```json
{
  "alarm": 5000,
  "machineStatus": 5000,
  "count": 5000,
  "currentToolNo": 5000,
  "energyConsumption": 60000,
  "feedAndSpindle": 5000,
  "laserPower": 5000,
  "load": 5000,
  "logHistory": 5000,
  "overload": 100,
  "plcData": 5000,
  "position": 5000,
  "currentProgramBlock": 5000,
  "programInfo": 5000,
  "machineTimeData": 5000,
  "toolLife": 5000,
  "toolOffset": 5000,
  "alarmHistory": 5000,
  "cycleData": 5000,
  "oee": 5000
}
```

**表格 63 机台任务间隔设置参数**

| 参数 | 类型 | 说明 |
|------|------|------|
| alarm | Int32 | 警报信息间隔（毫秒） |
| machineStatus | Int32 | 机台状态间隔（毫秒） |
| count | Int32 | 加工计数间隔（毫秒） |
| currentToolNo | Int32 | 当前刀号间隔（毫秒） |
| energyConsumption | Int32 | 能耗间隔（毫秒） |
| feedAndSpindle | Int32 | 进给转速数据间隔（毫秒） |
| laserPower | Int32 | 激光功率间隔（毫秒） |
| load | Int32 | 负载数据间隔（毫秒） |
| logHistory | Int32 | 日志历史间隔（毫秒） |
| overload | Int32 | 过载监控间隔（毫秒） |
| plcData | Int32 | PLC 数据间隔（毫秒） |
| position | Int32 | 坐标数据间隔（毫秒） |
| currentProgramBlock | Int32 | 当前程序段间隔（毫秒） |
| programInfo | Int32 | 加工程序信息间隔（毫秒） |
| machineTimeData | Int32 | 机台时间数据间隔（毫秒） |
| toolLife | Int32 | 刀具寿命间隔（毫秒） |
| toolOffset | Int32 | 刀具补偿间隔（毫秒） |
| alarmHistory | Int32 | 警报历史间隔（毫秒） |
| cycleData | Int32 | 节拍数据间隔（毫秒） |
| oee | Int32 | OEE 监控间隔（毫秒） |

---

### 2.9.5.2 update-machine-task-interval-settings 修改机台任务间隔设置

修改机台任务间隔设置并返回修改后的设置。

```
POST  /api/config/update-machine-task-interval-settings
```

**请求体示例**

```json
{
  "alarm": 6000,
  "machineStatus": 8000
}
```

请求参数见表格 63 机台任务间隔设置参数。不在请求体中的参数保持原来数值。

返回参数见表格 63 机台任务间隔设置参数。

---

### 2.9.5.3 group-task-interval-settings 获取机组任务间隔设置

此接口无请求参数。

```
GET  /api/config/group-task-interval-settings
```

**返回示例**

```json
{
  "count": 5000,
  "oee": 5000,
  "cumulativeStatusTime": 5000
}
```

**表格 64 机组任务间隔设置参数**

| 参数 | 类型 | 说明 |
|------|------|------|
| count | Int32 | 加工计数间隔（毫秒） |
| oee | Int32 | OEE 监控间隔（毫秒） |
| cumulativeStatusTime | Int32 | 累计状态时间（毫秒） |

---

### 2.9.5.4 update-group-task-interval-settings 修改机组任务间隔设置

修改机组任务间隔设置并返回修改后的设置。

```
POST  /api/config/update-group-task-interval-settings
```

**请求体示例**

```json
{
  "count": 3000,
  "oee": 2000,
  "cumulativeStatusTime": 1000
}
```

请求参数见表格 64 机组任务间隔设置参数。

返回参数见表格 64 机组任务间隔设置参数。

---

### 2.9.5.5 oee-monitoring-settings 获取 OEE 监控设置

此接口无请求参数。

```
GET  /api/config/oee-monitoring-settings
```

**返回示例**

```json
{
  "monitorMode": "Scheduled Reset",
  "windowSize": 3600,
  "resetTime": "00:00",
  "availabilityMode": "Autorun Time / Total Time",
  "breakTimeInterval": ""
}
```

**表格 65 OEE 监控设置参数**

| 参数 | 类型 | 说明 |
|------|------|------|
| monitorMode | String | 监控模式，详见表格 66 monitorMode 监控模式 |
| windowSize | Int32 | 窗口时间（秒），实时窗口模式下生效 |
| resetTime | String | 重置时刻（小时:分钟），到点重置模式下生效 |
| availabilityMode | String | 开动率计算方式，详见表格 67 availabilityMode 开动率计算方式 |
| breakTimeInterval | String | 休息时间段（小时:分钟-小时:分钟） |

**表格 66 monitorMode 监控模式**

| 选项 | 说明 |
|------|------|
| Scheduled Reset | 到点重置 |
| Time Window | 实时窗口 |

**表格 67 availabilityMode 开动率计算方式**

| 选项 | 说明 |
|------|------|
| Autorun Time / Total Time | 运行时间/总时间 |
| Autorun Time / (Total Time - Off Time) | 运行时间/（总时间-关机时间） |
| Autorun Time / (Total Time - Off Time - Manual Time) | 运行时间/（总时间-关机时间-调机时间） |
| Autorun Time / (Total Time - Break Time) | 运行时间/（总时间-休息时间） |

---

### 2.9.5.6 update-oee-monitoring-settings 修改 OEE 监控设置

修改 OEE 监控设置并返回修改后的设置。

```
POST  /api/config/update-oee-monitoring-settings
```

**请求体示例**

```json
{
  "monitorMode": "Time Window",
  "windowSize": 6000,
  "resetTime": "00:00;22:00;01:00;2:00",
  "availabilityMode": "Autorun Time / (Total Time - Break Time)",
  "breakTimeInterval": "08:00-09:00;11:30-12:30"
}
```

请求/返回参数见表格 65 OEE 监控设置参数。

---

### 2.9.5.7 tool-life-monitoring-settings 获取刀具寿命监控设置

此接口无请求参数。

```
GET  /api/config/tool-life-monitoring-settings
```

**返回示例**

```json
{
  "enableToolLifeDetails": false
}
```

**表格 68 刀具寿命监控设置参数**

| 参数 | 类型 | 说明 |
|------|------|------|
| enableToolLifeDetails | Bool | 刀具寿命详情，true=开启，false=关闭 |

---

### 2.9.5.8 update-tool-life-monitoring-settings 修改刀具寿命监控设置

修改刀具寿命监控设置并返回修改后的设置。

```
POST  /api/config/update-tool-life-monitoring-settings
```

**请求/返回参数见表格 68 刀具寿命监控设置参数。**

---

### 2.9.5.9 count-monitoring-settings 获取加工计数监控设置

此接口无请求参数。

```
GET  /api/config/count-monitoring-settings
```

**返回示例**

```json
{
  "monitorMode": "Time Window",
  "windowSize": 3600,
  "resetTime": "00:00"
}
```

**表格 69 加工计数监控设置参数**

| 参数 | 类型 | 说明 |
|------|------|------|
| monitorMode | String | 监控模式，详见表格 66 monitorMode 监控模式 |
| windowSize | Int32 | 窗口时间（秒），实时窗口模式下生效 |
| resetTime | String[] | 重置时刻（小时:分钟），到点重置模式下生效 |

---

### 2.9.5.10 update-count-monitoring-settings 修改加工计数监控设置

修改加工计数监控设置并返回修改后的设置。

```
POST  /api/config/update-count-monitoring-settings
```

**请求体示例**

```json
{
  "monitorMode": "Scheduled Reset",
  "windowSize": 36000,
  "resetTime": "23:00;3:24;15:28;12:22"
}
```

请求/返回参数见表格 69 加工计数监控设置参数。

---

### 2.9.5.11 overload-monitoring-settings 获取过载监控设置

此接口无请求参数。

```
GET  /api/config/overload-monitoring-settings
```

**返回示例**

```json
{
  "spindleLoadUpperLimit": 100.0
}
```

**表格 70 过载监控设置参数**

| 参数 | 类型 | 说明 |
|------|------|------|
| spindleLoadUpperLimit | Float | 主轴负载上限（%） |

---

### 2.9.5.12 update-overload-monitoring-settings 修改过载监控设置

修改过载监控设置并返回修改后的设置。

```
POST  /api/config/update-overload-monitoring-settings
```

**请求体示例**

```json
{
  "spindleLoadUpperLimit": 80.0
}
```

请求/返回参数见表格 70 过载监控设置参数。

---

### 2.9.5.13 alarm-monitoring-settings 获取警报监控设置

此接口无请求参数。

```
GET  /api/config/alarm-monitoring-settings
```

**返回示例**

```json
{
  "mininumAlarmLevel": "Warning"
}
```

**表格 71 警报监控设置参数**

| 参数 | 类型 | 说明 |
|------|------|------|
| mininumAlarmLevel | String | 最低报警级别，详见表格 72 mininumAlarmLevel 最低报警级别 |
| infoLevel | String | 消息级别关键词，不返回代表未设置 |
| errorLevel | String | 错误级别关键词，不返回代表未设置 |

**表格 72 mininumAlarmLevel 最低报警级别**

| 选项 | 说明 |
|------|------|
| Information | 消息 |
| Warning | 警告 |
| Error | 错误 |

---

### 2.9.5.14 update-alarm-monitoring-settings 修改警报监控设置

修改警报监控设置并返回修改后的设置。

```
POST  /api/config/update-alarm-monitoring-settings
```

**请求体示例**

```json
{
  "mininumAlarmLevel": "Error",
  "infoLevel": "消息;提示;Inf",
  "errorLevel": "错误;错误;Err"
}
```

请求/返回参数见表格 71 警报监控设置参数。

---

### 2.9.5.15 machine-status-monitoring-settings 获取机台状态监控设置

此接口无请求参数。

```
GET  /api/config/machine-status-monitoring-settings
```

**返回示例**

```json
{
  "enableStatusDetails": false,
  "enableAdjustedManualStatus": false,
  "maxManualPauseTime": 600,
  "enableStatusConversion": false
}
```

**表格 73 机台状态监控设置参数**

| 参数 | 类型 | 说明 |
|------|------|------|
| enableStatusDetails | Bool | 状态详情，true=开启，false=关闭 |
| enableAdjustedManualStatus | Bool | 调机状态修正，true=开启，false=关闭 |
| maxManualPauseTime | Int32 | 最长调机暂停时间（秒） |
| enableStatusConversion | Bool | 转换状态，true=开启，false=关闭 |
| statusConversionSettings | String | 状态转换，不返回代表未设置 |

---

### 2.9.5.16 update-machine-status-monitoring-settings 修改机台状态监控设置

修改机台状态监控设置并返回修改后的设置。

```
POST  /api/config/update-machine-status-monitoring-settings
```

**请求体示例**

```json
{
  "enableStatusDetails": true,
  "enableAdjustedManualStatus": true,
  "maxManualPauseTime": 300,
  "enableStatusConversion": true,
  "statusConversionSettings": "CNCStatus_cncStatus = \"MANUAL_MDI_RUN\" | AUTO_RUN"
}
```

请求/返回参数见表格 73 机台状态监控设置参数。

---

## 2.9.6 通讯配置接口

通讯配置接口用于获取或修改任务配置。通讯配置相关说明可以参考《说明书》5.6 通讯配置。

### 2.9.6.1 cloud-settings 获取云平台设置

此接口无请求参数。

```
GET  /api/config/cloud-settings
```

**返回示例**

```json
{
  "enable": false,
  "serverAddress": "severAddress",
  "token": "tokenABCDEFG",
  "enableBroadcasting": false,
  "enableGatewayLinking": false,
  "gatewayLinks": "uid1,token1;uid2,token2;"
}
```

**表格 74 云平台设置参数**

| 参数 | 类型 | 说明 |
|------|------|------|
| enable | Bool | 启用云平台，true=开启，false=关闭 |
| serverAddress | String | 服务器地址。不返回代表未设置 |
| token | String | 网关令牌。不返回代表未设置 |
| enableBroadcasting | Bool | 启用广播，true=开启，false=关闭。启用后通过云平台广播数据 |
| enableGatewayLinking | Bool | 启用网关关联，true=开启，false=关闭。启用后接收云平台下指定关联网关广播的数据 |
| gatewayLinks | String | 关联的网关。不返回代表未设置 |

---

### 2.9.6.2 update-cloud-settings 修改云平台设置

修改云平台设置并返回修改后的设置。

```
POST  /api/config/update-cloud-settings
```

请求/返回参数见表格 74 云平台设置参数。

---

### 2.9.6.3 modbus-settings 获取 MODBUS 设置

此接口无请求参数。

```
GET  /api/config/modbus-settings
```

**返回示例**

```json
{
  "enable": false,
  "byte4Order": "DCBA",
  "byte8Order": "HGFEDCBA",
  "encoding": "GBK",
  "reverseString": false
}
```

**表格 75 MODBUS 设置参数**

| 参数 | 类型 | 说明 |
|------|------|------|
| enable | Bool | 启用 MODBUS，true=开启，false=关闭 |
| byte4Order | String | 32 位型格式，支持：ABCD, BADC, CDAB, DCBA 等格式 |
| byte8Order | String | 64 位型格式，支持：ABCDEFGH, BADCFEHG, GHEFCDAB, HGFEDCBA 等格式 |
| encoding | String | 编码，如 ASCII, UTF-8, GBK 等 |
| reverseString | Bool | 反转字符串，true=开启，false=关闭 |

---

### 2.9.6.4 update-modbus-settings 修改 MODBUS 设置

修改 MODBUS 设置并返回修改后的设置。

```
POST  /api/config/update-modbus-settings
```

请求/返回参数见表格 75 MODBUS 设置参数。

---

### 2.9.6.5 mqtt-settings 获取 MQTT 设置

此接口无请求参数。

```
GET  /api/config/mqtt-settings
```

**返回示例**

```json
{
  "enable": false,
  "mode": "Default",
  "brokerAddress": "brokerAddress",
  "port": 1111,
  "clientID": "client",
  "username": "username",
  "password": "password",
  "dataReportTopic": "topic1",
  "rpcRequestTopic": "rpcReq",
  "rpcResponseTopic": "rpcRes",
  "encoding": "GBK",
  "allowAnomynous": false,
  "arrayToString": false,
  "publishOnValueChange": false,
  "publishAllOnValueChange": false,
  "timeoutReportingInterval": 0
}
```

**表格 76 MQTT 设置参数**

| 参数 | 类型 | 说明 |
|------|------|------|
| enable | Bool | 启用 MQTT，true=开启，false=关闭 |
| mode | String | 模式，详见表格 77 MQTT mode 模式 |
| brokerAddress | String | 服务器地址 |
| port | Int32 | 服务器端口 |
| clientID | String | 客户端 ID |
| username | String | 用户名 |
| password | String | 密码 |
| dataReportTopic | String | 数据上报主题 |
| rpcRequestTopic | String | RPC 请求主题 |
| rpcResponseTopic | String | RPC 回复主题 |
| encoding | String | 编码，如 ASCII, UTF-8, GBK 等 |
| allowAnomynous | Bool | 匿名登录，true=开启，false=关闭 |
| arrayToString | Bool | 数组转字符串，true=开启，false=关闭 |
| publishOnValueChange | Bool | 变化值写入，true=开启，false=关闭。默认为 false |
| publishAllOnValueChange | Bool | 变化时上传全部内容，true=开启，false=关闭。启用变化值上传时有效，默认为 false，即变化时，仅上传变化的部分 |
| timeoutReportingInterval | Int32 | 超时上报间隔。启用变化值上传时有效。单位：秒。默认为 0，即关闭超时上报 |

**表格 77 MQTT mode 模式**

| 选项 |
|------|
| Default |
| TB2 |
| Brm |
| IoTDA |
| WisIoT |
| MKT |
| TB |

---

### 2.9.6.6 update-mqtt-settings 修改 MQTT 设置

修改 MQTT 设置并返回修改后的设置。

```
POST  /api/config/update-mqtt-settings
```

请求/返回参数见表格 76 MQTT 设置参数。

---

### 2.9.6.7 database-settings 获取数据库设置

此接口无请求参数。

```
GET  /api/config/database-settings
```

**InfluxDB v2.x 类型返回示例**

```json
{
  "enable": true,
  "type": "InfluxDB v2.x",
  "serverAddress": "192.168.100.101",
  "port": "12345",
  "username": "username",
  "password": "password",
  "bucket": "Bucket",
  "tablePrefix": "Prefix",
  "organization": "Organization",
  "writeOnValueChange": true,
  "timeoutReportingInterval": 0
}
```

**非 InfluxDB v2.x 类型返回示例**

```json
{
  "enable": true,
  "type": "MySQL",
  "serverAddress": "192.168.100.101",
  "port": "12345",
  "username": "username",
  "password": "password",
  "database": "Database",
  "tablePrefix": "Prefix",
  "storageMode": "User Defined",
  "useLocalTime": false,
  "enablePrimaryKey": false,
  "writeOnValueChange": false,
  "timeoutReportingInterval": 0,
  "loggingModeTypes": ["AlarmHistory", "AxialOverload", "CNCStatus", "Count", ...],
  "realTimeModeTypes": ["AlarmLog", "GroupOEE", "OEE"]
}
```

**表格 78 数据库设置参数**

| 参数 | 类型 | 说明 |
|------|------|------|
| enable | Bool | 启用数据库，true=开启，false=关闭 |
| type | String | 类型，详见表格 79 数据库类型 |
| serverAddress | String | 服务器地址 |
| port | String | 服务器端口 |
| username | String | 用户名 |
| password | String | 密码 |
| bucket | String | 数据库，限 InfluxDB v2.x 类型 |
| organization | String | 机构名，限 InfluxDB v2.x 类型 |
| database | String | 数据库，限非 InfluxDB v2.x 类型 |
| storageMode | String | 保存模式，限非 InfluxDB v2.x 类型，详见表格 80 数据库保存模式 |
| dataRetentionPeriod | Int32 | 数据保留期限（小时），限非 InfluxDB v2.x 数据库类型 |
| loggingModeTypes | String[] | 保存模式为 User Defined 时有效，详见 1.2 数据说明中的 type 数据类型 |
| noActionModeTypes | String[] | 保存模式为 User Defined 时有效，详见 1.2 数据说明中的 type 数据类型 |
| realTimeModeTypes | String[] | 保存模式为 User Defined 时有效，详见 1.2 数据说明中的 type 数据类型 |
| useLocalTime | Bool | 使用本地时间，限非 InfluxDB v2.x 类型 |
| enablePrimaryKey | Bool | 启用主键，限非 InfluxDB v2.x 类型 |
| tablePrefix | String | 表前缀 |
| writeOnValueChange | Bool | 变化值写入，true=开启，false=关闭 |
| timeoutReportingInterval | Int32 | 超时上报间隔。启用变化值写入时有效。单位：秒。默认为 0，即关闭超时写入 |

**表格 79 数据库类型**

| 选项 |
|------|
| InfluxDB v2.x |
| MySQL |
| SQL Server |
| Postgre SQL |

**表格 80 数据库保存模式**

| 选项 | 说明 |
|------|------|
| Logging | 日志 |
| Real Time | 实时 |
| User Defined | 自定义 |

---

### 2.9.6.8 update-database-settings 修改数据库设置

修改数据库设置并返回修改后的设置。

```
POST  /api/config/update-database-settings
```

请求/返回参数见表格 78 数据库设置参数。

---

### 2.9.6.9 gateway-file-server-settings 获取网关文件服务器设置

此接口无请求参数。

```
GET  /api/config/gateway-file-server-settings
```

**返回示例**

```json
{
  "enable": false,
  "enableFtp": false,
  "enableSmb": false,
  "username": "dncuser",
  "password": "dncuser"
}
```

**表格 81 网关文件服务器设置参数**

| 参数 | 类型 | 说明 |
|------|------|------|
| enable | Bool | 启用网关文件服务器，true=开启，false=关闭 |
| enableFtp | String | 启用 FTP，true=开启，false=关闭 |
| enableSmb | String | 启用共享文件夹，true=开启，false=关闭 |
| username | String | 用户名，只读，不可修改 |
| password | String | 密码 |

---

### 2.9.6.10 update-gateway-file-server-settings 修改网关服务器设置

修改网关文件服务器设置并返回修改后的设置。

```
POST  /api/config/update-gateway-file-server-settings
```

请求/返回参数见表格 81 网关文件服务器设置参数。

---

### 2.9.6.11 http-settings 获取 HTTP 设置

此接口无请求参数。

```
GET  /api/config/http-settings
```

**返回示例**

```json
{
  "enable": true,
  "enableIpWhiteList": true,
  "ipWhiteList": "192.168.100.200;192.168.100.201;"
}
```

**表格 82 HTTP 设置参数**

| 参数 | 类型 | 说明 |
|------|------|------|
| Enable | Bool | 启用 HTTP，true=开启，false=关闭 |
| enableIpWhiteList | Bool | 启用 IP 白名单，true=开启，false=关闭 |
| ipWhiteList | String | 白名单列表（以;分隔）。未返回代表未设置 |

---

### 2.9.6.12 update-http-settings 修改 HTTP 设置

修改 HTTP 设置并返回修改后的设置。

```
POST  /api/config/update-http-settings
```

请求/返回参数见表格 82 HTTP 设置参数。

---

### 2.9.6.13 hub-settings 获取 Hub 设置

此接口无请求参数。

```
GET  /api/config/hub-settings
```

**返回示例**

```json
{
  "enable": false,
  "server": "serverAddress",
  "accessToken": "accessToken",
  "enableBroadcasting": false,
  "enableGatewayLinking": false,
  "gatewayLinks": "uid1,token1;uid2,token2;"
}
```

**表格 83 Hub 设置参数**

| 参数 | 类型 | 说明 |
|------|------|------|
| enable | Bool | 启用 Hub，true=开启，false=关闭 |
| server | String | 服务器地址。不返回代表未设置 |
| accessToken | String | 网关令牌。不返回代表未设置 |
| enableBroadcasting | Bool | 启用广播，true=开启，false=关闭。启用后通过 Hub 广播数据 |
| enableGatewayLinking | Bool | 启用网关关联，true=开启，false=关闭。启用后接收 Hub 下指定关联网关广播的数据 |
| gatewayLinks | String | 关联的网关。不返回代表未设置 |

---

### 2.9.6.14 update-hub-settings 修改 Hub 设置

修改 Hub 设置并返回修改后的设置。

```
POST  /api/config/update-hub-settings
```

请求/返回参数见表格 83 Hub 设置参数。

---

### 2.9.6.15 remote-access 获取远程访问设置

此接口无请求参数。

```
GET  /api/config/remote-access
```

**返回示例**

```json
{
  "host": "serverUrl",
  "hub": "XXXXXX-XXXXXX-XXXXXX-XXXXXX",
  "password": "password",
  "bridgeNetwork": "LAN1",
  "state": "Established",
  "expiration": "2999-12-31T23:59:59"
}
```

**表格 84 远程访问设置参数**

| 参数 | 类型 | 说明 |
|------|------|------|
| host | String | 远程服务器地址 |
| hub | String | 站点（只读），默认为网关 UID |
| password | String | 密码 |
| bridgeNetwork | String | 桥接网络："LAN1"，"LAN2"，""（无桥接） |
| state | String | 连接状态（只读）：Connecting, Negotiation, Retry（以上三种状态均为连接中状态），Auth（验证中），Established（已连接），Idle（未连接） |
| expiration | String | 截止日期（只读），仅当 state 为 Established（已连接）状态时返回 |

---

### 2.9.6.16 update-remote-access 修改远程访问设置

修改远程访问设置并返回修改后的设置。

```
POST  /api/config/update-remote-access
```

**请求体示例**

```json
{
  "host": "serverUrl2",
  "password": "password2",
  "bridgeNetwork": "LAN1"
}
```

请求/返回参数见表格 84 远程访问设置参数。
