# Chapter II — HTTP Communication (Continued)

## 2.9 Gateway Configuration Interfaces

The gateway configuration interfaces are used to command the gateway to perform specific operations. The base path is `/api/config`.

---

## 2.9.1 Global Configuration Interfaces

### 2.9.1.1 gateway-info — Get Gateway Information

This interface has no request parameters.

```
GET  /api/config/gateway-info
```

**Response Example**

```json
{
  "hid": "XXXXXX-XXXXXX-XXXXXX-XXXXXX",
  "uid": "XXXXXX-XXXXXX-XXXXXX-XXXXXX",
  "alias": "iotgw"
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| hid | String | (Required) Gateway hardware ID |
| uid | String | (Required) Gateway UID |
| alias | String | (Required) Gateway name |

---

### 2.9.1.2 settings — Get Global Gateway Settings

This interface has no request parameters.

```
GET  /api/config/settings
```

**Response Example**

```json
{
  "version": "1.19.3.102",
  "enableLocalCaching": true,
  "enableRemoteService": true,
  "enableLan2Setup": false,
  "timeServerAddress": "ntp1.aliyun.com;ntp2.aliyun.com;ntp3.aliyun.com;ntp4.aliyun.com;"
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| version | String | (Required) Settings version |
| enableLocalCaching | Bool | (Required) Enable local cache |
| enableRemoteService | Bool | (Required) Enable remote assistance |
| enableLan2Setup | Bool | (Required) Enable LAN2 configuration |
| timeServerAddress | String | (Required) Time server address |

---

### 2.9.1.3 update-settings — Modify Global Gateway Settings

```
POST  /api/gateway/update-settings
```

**Request Body Example** (application/json)

```json
{
  "enableLocalCaching": true,
  "enableRemoteService": true,
  "enableLan2Setup": false,
  "timeServerAddress": "ntp1.aliyun.com;ntp2.aliyun.com;ntp3.aliyun.com;ntp4.aliyun.com;"
}
```

| Request Parameter | Type | Description |
|-------------------|------|-------------|
| enableLocalCaching | Bool | Enable local cache |
| enableRemoteService | Bool | Enable remote assistance |
| enableLan2Setup | Bool | Enable LAN2 configuration |
| timeServerAddress | String | Time server address |

**Response Example**

```json
{
  "version": "1.19.3.102",
  "enableLocalCaching": true,
  "enableRemoteService": true,
  "enableLan2Setup": false,
  "timeServerAddress": "ntp1.aliyun.com;ntp2.aliyun.com;ntp3.aliyun.com;ntp4.aliyun.com;"
}
```

Response parameters are the same as request parameters, with the addition of `version` (settings version).

---

### 2.9.1.4 security — Get Global Security Settings

This interface has no request parameters.

```
GET  /api/config/security
```

**Response Example**

```json
{
  "enable": true,
  "protectedApi": "exact,/cnc/writeOffsetData;exact,/cnc/writePlcData"
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| enable | Bool | (Required) Enable global security control |
| protectedApi | String | Protected APIs — APIs that no user is permitted to call. Not returned if not configured. See Table 58 for API command format. |

**Table 58 — API Command Format**

| Command Type | `exact` | `prefix` |
|-------------|---------|---------|
| Description | Specifies one exact API path | Specifies all API paths sharing a given prefix |
| Example | `exact,/cnc/writePlcData` | `prefix,/db` |

- API paths begin after `/api`. The value `"prefix,/"` matches all API paths.
- Multiple API commands are separated by `;`.

---

### 2.9.1.5 update-security — Modify Global Security Settings

```
POST  /api/gateway/update-security
```

**Request Body Example** (application/json)

```json
{
  "enable": true,
  "protectedApi": "exact,/cnc/writeOffsetData;exact,/cnc/writePlcData"
}
```

| Request Parameter | Type | Description |
|-------------------|------|-------------|
| enable | Bool | (Required) Enable global security control |
| protectedApi | String | Protected APIs. See Table 58 for API command format. |

**Response Example**

```json
{
  "enable": true,
  "protectedApi": "exact,/cnc/writeOffsetData;exact,/cnc/writePlcData"
}
```

Response parameters are the same as request parameters.

---

## 2.9.2 User Configuration Interfaces

### 2.9.2.1 user — Get User Settings

This interface has no request parameters. Returns the settings for the currently authenticated user.

```
GET  /api/config/user
```

**Response Example**

```json
{
  "id": 0,
  "username": "admin",
  "isAdmin": true
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| id | Int32 | (Required) Database identifier; see section 1.1.4 ID — Database Identifier |
| username | String | (Required) Username |
| isAdmin | Bool | (Required) Whether the user has administrator privileges |

---

### 2.9.2.2 users — Get All User Settings

This interface has no request parameters. Returns settings for all users.

```
GET  /api/config/users
```

**Response Example**

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

Response parameters are the same as section 2.9.2.1.

---

### 2.9.2.3 create-user — Create New User

The new user's initial password is the same as the username.

```
POST  /api/config/create-user
```

**Request Body Example** (application/json)

```json
{
  "username": "user2",
  "isAdmin": false
}
```

| Request Parameter | Type | Description |
|-------------------|------|-------------|
| username | String | (Required) Username |
| isAdmin | Bool | Whether the user has administrator privileges. Defaults to `true`. |

**Response Example**

```json
{
  "id": 2,
  "username": "user2",
  "isAdmin": false
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| id | Int32 | (Required) Database identifier |
| username | String | (Required) Username |
| isAdmin | Bool | (Required) Whether the user has administrator privileges |

---

### 2.9.2.4 update-user — Modify User Settings

When the username is changed, the password is not affected.

```
POST  /api/config/update-user
```

**Request Body Example** (application/json)

```json
{
  "id": 2,
  "username": "user2",
  "isAdmin": false
}
```

| Request Parameter | Type | Description |
|-------------------|------|-------------|
| id | Int32 | (Required) Database identifier |
| username | String | Username. Not modified if omitted. |
| isAdmin | Bool | Whether the user has administrator privileges. Not modified if omitted. |

**Response Example**

```json
{
  "id": 2,
  "username": "user2",
  "isAdmin": false
}
```

Response parameters are the same as section 2.9.2.3.

---

### 2.9.2.5 delete-user — Delete User

```
GET  /api/config/delete-user?id=ID
```

| Request Parameter | Type | Description |
|-------------------|------|-------------|
| id | Int32 | (Required) Database identifier |

**Response Example**

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

---

### 2.9.2.6 user-security — Get User Security Settings

```
GET  /api/config/user-security?id=ID
```

| Request Parameter | Type | Description |
|-------------------|------|-------------|
| id | Int32 | (Required) Database identifier |

**Response Example**

```json
{
  "id": 2,
  "secretKey": "sk-XXXXXXXXXX",
  "authorizedApis": "prefix,/cnc;prefix,/db;prefix,/analysis;prefix,/group-analysis;prefix,/config;prefix,/core;prefix,/gateway;prefix,/auth;"
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| id | Int32 | (Required) Database identifier |
| secretKey | String | Secret key, prefixed with `sk-`. Not returned if not configured. |
| authorizedApis | String | Authorized APIs — APIs the user is permitted to call. Not returned if not configured. See Table 58 for API command format. |

---

### 2.9.2.7 update-user-security — Modify User Security Settings

```
POST  /api/config/update-user-security
```

**Request Body Example** (application/json)

```json
{
  "id": 2,
  "secretKey": "Token",
  "authorizedApis": "prefix,/cnc;prefix,/db;prefix,/analysis;prefix,/group-analysis;prefix,/config;prefix,/core;prefix,/gateway;prefix,/auth;"
}
```

| Request Parameter | Type | Description |
|-------------------|------|-------------|
| id | Int32 | (Required) Database identifier |
| secretKey | String | Secret key, prefixed with `sk-` |
| authorizedApis | String | Authorized APIs. See Table 58 for API command format. |

**Response Example**

```json
{
  "id": 2,
  "secretKey": "Token",
  "authorizedApis": "prefix,/cnc;prefix,/db;prefix,/analysis;prefix,/group-analysis;prefix,/config;prefix,/core;prefix,/gateway;prefix,/auth;"
}
```

Response parameters are the same as request parameters.

---

## 2.9.3 Machine Configuration Interfaces

The machine configuration interfaces are used to get, create, modify, or delete machine configurations. For background on machine configuration, refer to section 5.3 Machine Configuration in the product manual.

**Table 59 — Machine Configuration Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| id | Int32 | Database identifier |
| machineType | String | Machine type. See Table 60 — machineType Values. |
| system | String | System |
| model | String | Model |
| name | String | Machine name |
| machineID | String | Machine identifier |
| slaveID | Int32 | Slave station identifier |
| ip | String | IP address |
| port | Int32 | Port number. `0` = use the device's default port. |
| isActive | Bool | Active status. `true` = active, `false` = inactive. |
| useDefaultMachineID | Bool | Use the default machine identifier |
| useDefaultSlaveID | Bool | Use the default slave station identifier |
| customAttributes | String | Custom attributes. Example: `{"attribute": value,...}` |
| username | String | Username. Required by some devices. |
| permission | String | Permission level. Required only for Delta CNC systems. Values: `NORMAL`, `USER_1`, `USER_2`, `MACHIN`, `SYSTEM`. Default: `NORMAL`. |
| password | String | Password. Required by some devices. |
| version | String | Version. Required by some devices. |
| connectionMode | String | Connection mode. Required by some devices. |
| encoding | String | Character encoding (e.g., `ASCII`, `UTF-8`, `GBK`). Default: `default` (auto-detect). |
| language | String | Alarm language. Required only for Siemens systems. Supported values: `English`, `Chinese`. Default: `Chinese`. |
| numberOfTasks | Int32 | Number of enabled tasks. Read-only. |
| taskAlarm | Bool | Alarm information task. `true` = enabled, `false` = disabled. |
| taskAlarmPrecond | String | Alarm task precondition. Not returned if not configured. |
| taskAlarmEnableCustomAlarm | Bool | Custom alarm. `true` = enabled. When enabled, replaces default alarms with custom alarms. |
| taskAlarmCustomAlarmStatusTag | String | Custom alarm `alarmStatus` tag. Enter the corresponding PLC data task result tag. |
| taskAlarmCustomAlarmMsgTag | String | Custom alarm `alarmMsg` tag. Enter the corresponding PLC data task result tag. |
| taskMachineStatus | Bool | Machine status task. `true` = enabled. |
| taskChannelMachineStatus | String | Machine status task target channels. Example: `"0;1-3"`. |
| taskMachineStatusPrecond | String | Machine status task precondition. Not returned if not configured. |
| taskMachineStatusEnableAdjustedManual | Bool | Machine status task setup-mode correction. `true` = enabled. |
| taskMachineStatusMaxManualPauseTime | Int32 | Machine status task maximum setup-mode pause time [seconds]. |
| taskMachineStatusEnableStatusConversion | Bool | Machine status task status conversion. `true` = enabled. |
| taskMachineStatusStatusConversionSettings | String | Machine status task: status to convert when condition is met. |
| taskMachineStatusEnableCustomStatus | Bool | Custom machine status. `true` = enabled. When enabled, replaces default status with custom status. |
| taskMachineStatusCustomStatusCNCStatusTag | String | Custom status `cncStatus` tag. Enter the corresponding PLC data task result tag. |
| taskMachineStatusCustomStatusAlarmStatusTag | String | Custom status `alarmStatus` tag. Enter the corresponding PLC data task result tag. |
| taskCount | Bool | Machining count task. `true` = enabled. |
| taskCountPrecond | String | Machining count task precondition. Not returned if not configured. |
| taskCountEnableCustomCount | Bool | Custom machining count. `true` = enabled. |
| taskCountCustomCountTag | String | Custom machining count tag. Enter the corresponding PLC data task result tag. |
| taskCurrentToolNo | Bool | Current tool number task. `true` = enabled. |
| taskChannelCurrentToolNo | String | Current tool number task target channels. |
| taskCurrentToolNoPrecond | String | Current tool number task precondition. Not returned if not configured. |
| taskEnergyConsumption | Bool | Energy consumption task. `true` = enabled. |
| taskEnergyConsumptionPrecond | String | Energy consumption task precondition. Not returned if not configured. |
| taskFeed | Bool | Feed task. `true` = enabled. |
| taskChannelFeed | String | Feed task target channels. |
| taskFeedPrecond | String | Feed task precondition. Not returned if not configured. |
| taskFeedAndSpindle | Bool | Feed and spindle task. `true` = enabled. |
| taskChannelFeedAndSpindle | String | Feed and spindle task target channels. |
| taskFeedAndSpindlePrecond | String | Feed and spindle task precondition. Not returned if not configured. |
| taskLaserPower | Bool | Laser power task. `true` = enabled. |
| taskLoad | Bool | Load data task. `true` = enabled. |
| taskChannelLoad | String | Load data task target channels. |
| taskLoadPrecond | String | Load data task precondition. Not returned if not configured. |
| taskLogHistory | Bool | Log history task. `true` = enabled. |
| taskLogHistoryPrecond | String | Log history task precondition. Not returned if not configured. |
| taskOverLoad | Bool | Overload monitoring task. `true` = enabled. |
| taskChannelOverLoad | String | Overload monitoring task target channels. |
| taskOverLoadPrecond | String | Overload monitoring task precondition. Not returned if not configured. |
| taskPlcData | Bool | PLC data task. `true` = enabled. |
| taskPlcDataSettings | String | PLC data task settings. |
| taskPosition | Bool | Axis position task. `true` = enabled. |
| taskChannelPosition | String | Axis position task target channels. |
| taskPositionPrecond | String | Axis position task precondition. Not returned if not configured. |
| taskCurrentProgramBlock | Bool | Current program block task. `true` = enabled. |
| taskCurrentProgramBlockPrecond | String | Current program block task precondition. Not returned if not configured. |
| taskProgramInfo | Bool | Program information task. `true` = enabled. |
| taskChannelProgramInfo | String | Program information task target channels. |
| taskProgramInfoPrecond | String | Program information task precondition. Not returned if not configured. |
| taskMachineTimeData | Bool | Machine time data task. `true` = enabled. |
| taskMachineTimeDataPrecond | String | Machine time data task precondition. Not returned if not configured. |
| taskToolLife | Bool | Tool life task. `true` = enabled. |
| taskToolLifePrecond | String | Tool life task precondition. Not returned if not configured. |
| taskToolLifeGroupNumIndex | String | Tool life task target tools in `{groupNum.indexInGroup}` format. |
| taskToolLifeIndex | String | Tool life task target tools in `{index}` format. |
| taskToolLifeToolNum | String | Tool life task target tools in `{toolNum}` format. |
| taskToolLifeToolNumOffsetNum | String | Tool life task target tools in `{toolNum.offsetNum}` format. |
| taskToolOffset | Bool | Tool offset task. `true` = enabled. |
| taskChannelToolOffset | String | Tool offset task target channels. |
| taskToolOffsetPrecond | String | Tool offset task precondition. Not returned if not configured. |
| taskToolOffsetToolNum | String | Tool offset task target tools in `{toolNum}` format. |
| taskToolOffsetOffsetNum | String | Tool offset task target tools in `{offsetNum}` format. |
| taskToolOffsetToolNumOffsetNum | String | Tool offset task target tools in `{toolNum.offsetNum}` format. |
| taskAlarmHistory | Bool | Alarm history task. `true` = enabled. |
| taskAlarmHistoryPrecond | String | Alarm history task precondition. Not returned if not configured. |
| taskCycleData | Bool | Cycle time data task. `true` = enabled. |
| taskCycleDataPrecond | String | Cycle time data task precondition. Not returned if not configured. |
| taskOEE | Bool | OEE monitoring task. `true` = enabled. |
| taskOEEPrecond | String | OEE monitoring task precondition. Not returned if not configured. |
| taskExternalPlcDataSettings | String | External PLC task settings. |
| parallelProcessing | Int | Number of parallel task processing threads. |
| fileServerType | String | File server type (program transfer). Values: `Machine Memory`, `Shared Folder`, `Shared Folder (Win XP)`, `FTP Server`, `Wireless Disk`, `Gateway File Server`. |
| fileServerAddress | String | Server address (program transfer). |
| fileServerPort | Int32 | Custom port (program transfer). |
| fileServerUsername | String | Username (program transfer). |
| fileServerPassword | String | Password (program transfer). |
| fileServerUCode | String | U-code (program transfer). |
| fileServerRootDir | String | Root directory (program transfer). |
| modbusSlaveID | Int32 | Slave ID (standard), required for MODBUS communication. Default: `1`. |
| modbusByteOrder4 | String | 32-bit type byte order, required for MODBUS communication. Values: `DCBA`, `BADC`, `ABCD`, `CDAB`. |
| modbusByteOrder8 | String | 64-bit type byte order, required for MODBUS communication. Values: `HGFEDCBA`, `BADCFEHG`, `ABCDEFGH`, `GHEFCDAB`. |
| modbusPLCAddress | Bool | Use PLC addressing, required for MODBUS communication. `true` = enabled. When enabled, the target address is offset by +1. |
| modbusReverseString | Bool | Reverse string, required for MODBUS communication. `true` = enabled. |

> **Note:** The interface only returns parameters that have been set. The `password` parameter is never returned regardless of whether it has been set. Supported tasks differ by `machineType`; the interface returns task-related parameters only for tasks supported by the machine type.

**Table 60 — machineType Values**

| machineType | Machine Type |
|-------------|-------------|
| CNC | CNC Machine |
| LASER | Laser Cutter |
| PLC | PLC |
| ROBOT | Robot |

---

### 2.9.3.1 machine — Get Machine Configuration

```
GET  /api/config/machine?ID=ID&machineID=MACHINEID
```

| Request Parameter | Type | Description |
|-------------------|------|-------------|
| id | Int32 | Database identifier |
| machineID | String | Target machine identifier. If both `id` and `machineID` are supplied, `machineID` is ignored. |

**Response Example**

```json
{
  "id": 1,
  "machineType": "CNC",
  "system": "Mock",
  "model": "General",
  "name": "Demo 1",
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

Response parameters: see Table 59 — Machine Configuration Parameters.

---

### 2.9.3.2 machines — Get All Machine Configurations

This interface has no request parameters.

```
GET  /api/config/machines
```

Returns an array of all machine configurations. Response parameters: see Table 59 — Machine Configuration Parameters.

---

### 2.9.3.3 create-machine — Add Machine Configuration

```
POST  /api/config/create-machine
```

**Request Body Example**

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

Request parameters: see Table 59 — Machine Configuration Parameters. Note: do not include the database identifier `id` in the request body; the gateway assigns `id` automatically. The assigned `id` appears in the response body after successful creation.

Response parameters: see Table 59 — Machine Configuration Parameters.

---

### 2.9.3.4 update-machine — Modify Machine Configuration

Modifies the configuration of the specified machine and returns the updated configuration.

```
POST  /api/config/update-machine
```

**Request Body Example**

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

Request parameters: see Table 59 — Machine Configuration Parameters. Note: this interface uses `id` as the unique identifier; `id` must be included in the request body. The machine identifier `machineID` can also be changed through this interface.

Response parameters: see Table 59 — Machine Configuration Parameters.

---

### 2.9.3.5 delete-machine — Delete Machine Configuration

```
GET  /api/config/delete-machine?ID=ID&machineID=MACHINEID
```

| Request Parameter | Type | Description |
|-------------------|------|-------------|
| id | Int32 | Database identifier |
| machineID | String | Target machine identifier. If both `id` and `machineID` are supplied, `machineID` is ignored. |

**Response Example**

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

---

### 2.9.3.6 batch-delete-machines — Batch Delete Machine Configurations

This interface is the batch version of section 2.9.3.5 `delete-machine`. By specifying a list of database IDs in the request body, multiple machines can be deleted in a single call.

```
POST  /api/config/batch-delete-machines
```

**Request Body Example** (application/json)

```json
{
  "ids": [2, 3, 4]
}
```

| Request Parameter | Type | Description |
|-------------------|------|-------------|
| ids | Int32[] | (Required) Array of database identifiers |

**Response Example**

```json
{
  "deleted": 3
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| deleted | Int32 | (Required) Number of machines deleted |

---

## 2.9.4 Machine Group Configuration Interfaces

The machine group configuration interfaces are used to get, create, modify, or delete machine group configurations. For background information, refer to section 5.4 Machine Group Configuration in the product manual.

**Table 61 — Machine Group Configuration Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| id | Int32 | Machine group database identifier |
| number | Int32 | Machine group number. Valid range: 1–8. |
| useDefaultGroupID | Bool | Use the default machine group identifier |
| groupID | String | Machine group identifier |
| name | String | Machine group name |
| isActive | Bool | Active status. `true` = active, `false` = inactive. |
| machines | Object[] | List of machines in the group. See Table 62 — machines Machine List. |
| enableExternalMachines | Bool | Enable external machines |
| externalMachines | String | External machine command string |
| taskCount | Bool | Machine group machining count task. `true` = enabled. |
| taskOEE | Bool | Machine group OEE monitoring task. `true` = enabled. |
| taskCumulativeStatusTime | Bool | Machine group cumulative status time task. `true` = enabled. |

**Table 62 — machines Machine List**

| Parameter | Type | Description |
|-----------|------|-------------|
| id | Int32 | Machine database identifier. Add or remove `id` in the list to add or remove a machine from the group. |
| machineType | String | Machine type |
| system | String | System |
| model | String | Model |
| name | String | Machine name |
| machineID | String | Machine identifier. Add or remove `machineID` in the list to add or remove a machine from the group. If both `id` and `machineID` are supplied, `machineID` is ignored. |
| slaveID | Int32 | Slave station identifier |
| ip | String | IP address |
| port | Int32 | Port number. `0` = use the device's default port. |
| isActive | Bool | Active status |
| countMultiplier | Int32 | Production count multiplier |

---

### 2.9.4.1 group — Get Machine Group Configuration

```
GET  /api/config/group?ID=ID&groupID=GROUPID
```

| Request Parameter | Type | Description |
|-------------------|------|-------------|
| id | Int32 | Database identifier |
| groupID | String | Target machine group identifier. If both `id` and `groupID` are supplied, `groupID` is ignored. |

**Response Example**

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
      "name": "Demo 1",
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

Response parameters: see Table 61 — Machine Group Configuration Parameters.

---

### 2.9.4.2 groups — Get All Machine Group Configurations

This interface has no request parameters.

```
GET  /api/config/groups
```

Returns an array of all machine group configurations. Response parameters: see Table 61 — Machine Group Configuration Parameters.

---

### 2.9.4.3 create-group — Add Machine Group Configuration

```
POST  /api/config/create-group
```

**Request Body Example**

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

Request parameters: see Table 61 — Machine Group Configuration Parameters. Note: do not include `id` in the request body; the gateway assigns `id` automatically. The assigned `id` appears in the response body after successful creation.

Response parameters: see Table 61 — Machine Group Configuration Parameters.

---

### 2.9.4.4 update-group — Modify Machine Group Configuration

Modifies the configuration of the specified machine group and returns the updated configuration.

```
POST  /api/config/update-group
```

**Request Body Example**

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

Request parameters: see Table 61 — Machine Group Configuration Parameters. Note: this interface uses `id` as the unique identifier; `id` must be included in the request body. The group identifier `groupID` can also be changed through this interface.

Response parameters: see Table 61 — Machine Group Configuration Parameters.

---

### 2.9.4.5 delete-group — Delete Machine Group Configuration

```
GET  /api/config/delete-group?ID=ID&groupID=GROUPID
```

| Request Parameter | Type | Description |
|-------------------|------|-------------|
| ID | Int32 | Database identifier |
| groupID | String | Target machine group identifier. If both `ID` and `groupID` are supplied, `groupID` is ignored. |

**Response Example**

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

---

### 2.9.4.6 batch-delete-groups — Batch Delete Machine Group Configurations

This interface is the batch version of section 2.9.4.5 `delete-group`. By specifying a list of database IDs in the request body, multiple machine groups can be deleted in a single call.

```
POST  /api/config/batch-delete-groups
```

**Request Body Example** (application/json)

```json
{
  "ids": [2, 3, 4]
}
```

| Request Parameter | Type | Description |
|-------------------|------|-------------|
| ids | Int32[] | (Required) Array of database identifiers |

**Response Example**

```json
{
  "deleted": 3
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| deleted | Int32 | (Required) Number of machine groups deleted |

---

## 2.9.5 Task Configuration Interfaces

The task configuration interfaces are used to get or modify task configurations. For background information, refer to section 5.5 Task Configuration in the product manual.

### 2.9.5.1 machine-task-interval-settings — Get Machine Task Interval Settings

This interface has no request parameters.

```
GET  /api/config/machine-task-interval-settings
```

**Response Example**

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

**Table 63 — Machine Task Interval Settings Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| alarm | Int32 | Alarm information interval [ms] |
| machineStatus | Int32 | Machine status interval [ms] |
| count | Int32 | Machining count interval [ms] |
| currentToolNo | Int32 | Current tool number interval [ms] |
| energyConsumption | Int32 | Energy consumption interval [ms] |
| feedAndSpindle | Int32 | Feed and spindle data interval [ms] |
| laserPower | Int32 | Laser power interval [ms] |
| load | Int32 | Load data interval [ms] |
| logHistory | Int32 | Log history interval [ms] |
| overload | Int32 | Overload monitoring interval [ms] |
| plcData | Int32 | PLC data interval [ms] |
| position | Int32 | Axis position interval [ms] |
| currentProgramBlock | Int32 | Current program block interval [ms] |
| programInfo | Int32 | Program information interval [ms] |
| machineTimeData | Int32 | Machine time data interval [ms] |
| toolLife | Int32 | Tool life interval [ms] |
| toolOffset | Int32 | Tool offset interval [ms] |
| alarmHistory | Int32 | Alarm history interval [ms] |
| cycleData | Int32 | Cycle time data interval [ms] |
| oee | Int32 | OEE monitoring interval [ms] |

---

### 2.9.5.2 update-machine-task-interval-settings — Modify Machine Task Interval Settings

Modifies machine task interval settings and returns the updated settings.

```
POST  /api/config/update-machine-task-interval-settings
```

**Request Body Example**

```json
{
  "alarm": 6000,
  "machineStatus": 8000
}
```

Request parameters: see Table 63. Parameters not included in the request body retain their current values.

Response parameters: see Table 63.

---

### 2.9.5.3 group-task-interval-settings — Get Machine Group Task Interval Settings

This interface has no request parameters.

```
GET  /api/config/group-task-interval-settings
```

**Response Example**

```json
{
  "count": 5000,
  "oee": 5000,
  "cumulativeStatusTime": 5000
}
```

**Table 64 — Machine Group Task Interval Settings Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| count | Int32 | Machining count interval [ms] |
| oee | Int32 | OEE monitoring interval [ms] |
| cumulativeStatusTime | Int32 | Cumulative status time interval [ms] |

---

### 2.9.5.4 update-group-task-interval-settings — Modify Machine Group Task Interval Settings

Modifies machine group task interval settings and returns the updated settings.

```
POST  /api/config/update-group-task-interval-settings
```

**Request Body Example**

```json
{
  "count": 3000,
  "oee": 2000,
  "cumulativeStatusTime": 1000
}
```

Request/response parameters: see Table 64.

---

### 2.9.5.5 oee-monitoring-settings — Get OEE Monitoring Settings

This interface has no request parameters.

```
GET  /api/config/oee-monitoring-settings
```

**Response Example**

```json
{
  "monitorMode": "Scheduled Reset",
  "windowSize": 3600,
  "resetTime": "00:00",
  "availabilityMode": "Autorun Time / Total Time",
  "breakTimeInterval": ""
}
```

**Table 65 — OEE Monitoring Settings Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| monitorMode | String | Monitoring mode. See Table 66 — monitorMode Values. |
| windowSize | Int32 | Window duration [seconds]. Applies in Time Window mode. |
| resetTime | String | Reset time (HH:MM). Applies in Scheduled Reset mode. |
| availabilityMode | String | Availability calculation method. See Table 67 — availabilityMode Values. |
| breakTimeInterval | String | Break time intervals (HH:MM–HH:MM). |

**Table 66 — monitorMode Values**

| Option | Description |
|--------|-------------|
| Scheduled Reset | Reset at a scheduled time |
| Time Window | Real-time sliding window |

**Table 67 — availabilityMode Values**

| Option | Description |
|--------|-------------|
| Autorun Time / Total Time | Auto-run time / total time |
| Autorun Time / (Total Time - Off Time) | Auto-run time / (total time − off time) |
| Autorun Time / (Total Time - Off Time - Manual Time) | Auto-run time / (total time − off time − setup time) |
| Autorun Time / (Total Time - Break Time) | Auto-run time / (total time − break time) |

---

### 2.9.5.6 update-oee-monitoring-settings — Modify OEE Monitoring Settings

Modifies OEE monitoring settings and returns the updated settings.

```
POST  /api/config/update-oee-monitoring-settings
```

**Request Body Example**

```json
{
  "monitorMode": "Time Window",
  "windowSize": 6000,
  "resetTime": "00:00;22:00;01:00;2:00",
  "availabilityMode": "Autorun Time / (Total Time - Break Time)",
  "breakTimeInterval": "08:00-09:00;11:30-12:30"
}
```

Request/response parameters: see Table 65.

---

### 2.9.5.7 tool-life-monitoring-settings — Get Tool Life Monitoring Settings

This interface has no request parameters.

```
GET  /api/config/tool-life-monitoring-settings
```

**Response Example**

```json
{
  "enableToolLifeDetails": false
}
```

**Table 68 — Tool Life Monitoring Settings Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| enableToolLifeDetails | Bool | Tool life details. `true` = enabled, `false` = disabled. |

---

### 2.9.5.8 update-tool-life-monitoring-settings — Modify Tool Life Monitoring Settings

Modifies tool life monitoring settings and returns the updated settings.

```
POST  /api/config/update-tool-life-monitoring-settings
```

Request/response parameters: see Table 68.

---

### 2.9.5.9 count-monitoring-settings — Get Machining Count Monitoring Settings

This interface has no request parameters.

```
GET  /api/config/count-monitoring-settings
```

**Response Example**

```json
{
  "monitorMode": "Time Window",
  "windowSize": 3600,
  "resetTime": "00:00"
}
```

**Table 69 — Machining Count Monitoring Settings Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| monitorMode | String | Monitoring mode. See Table 66 — monitorMode Values. |
| windowSize | Int32 | Window duration [seconds]. Applies in Time Window mode. |
| resetTime | String[] | Reset time(s) (HH:MM). Applies in Scheduled Reset mode. |

---

### 2.9.5.10 update-count-monitoring-settings — Modify Machining Count Monitoring Settings

Modifies machining count monitoring settings and returns the updated settings.

```
POST  /api/config/update-count-monitoring-settings
```

**Request Body Example**

```json
{
  "monitorMode": "Scheduled Reset",
  "windowSize": 36000,
  "resetTime": "23:00;3:24;15:28;12:22"
}
```

Request/response parameters: see Table 69.

---

### 2.9.5.11 overload-monitoring-settings — Get Overload Monitoring Settings

This interface has no request parameters.

```
GET  /api/config/overload-monitoring-settings
```

**Response Example**

```json
{
  "spindleLoadUpperLimit": 100.0
}
```

**Table 70 — Overload Monitoring Settings Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| spindleLoadUpperLimit | Float | Spindle load upper limit [%] |

---

### 2.9.5.12 update-overload-monitoring-settings — Modify Overload Monitoring Settings

Modifies overload monitoring settings and returns the updated settings.

```
POST  /api/config/update-overload-monitoring-settings
```

**Request Body Example**

```json
{
  "spindleLoadUpperLimit": 80.0
}
```

Request/response parameters: see Table 70.

---

### 2.9.5.13 alarm-monitoring-settings — Get Alarm Monitoring Settings

This interface has no request parameters.

```
GET  /api/config/alarm-monitoring-settings
```

**Response Example**

```json
{
  "mininumAlarmLevel": "Warning"
}
```

**Table 71 — Alarm Monitoring Settings Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| mininumAlarmLevel | String | Minimum alarm level. See Table 72 — mininumAlarmLevel Values. |
| infoLevel | String | Information-level keywords. Not returned if not configured. |
| errorLevel | String | Error-level keywords. Not returned if not configured. |

**Table 72 — mininumAlarmLevel Values**

| Option | Description |
|--------|-------------|
| Information | Information |
| Warning | Warning |
| Error | Error |

---

### 2.9.5.14 update-alarm-monitoring-settings — Modify Alarm Monitoring Settings

Modifies alarm monitoring settings and returns the updated settings.

```
POST  /api/config/update-alarm-monitoring-settings
```

**Request Body Example**

```json
{
  "mininumAlarmLevel": "Error",
  "infoLevel": "Info;Inf;MSG",
  "errorLevel": "Error;ERR;Err"
}
```

Request/response parameters: see Table 71.

---

### 2.9.5.15 machine-status-monitoring-settings — Get Machine Status Monitoring Settings

This interface has no request parameters.

```
GET  /api/config/machine-status-monitoring-settings
```

**Response Example**

```json
{
  "enableStatusDetails": false,
  "enableAdjustedManualStatus": false,
  "maxManualPauseTime": 600,
  "enableStatusConversion": false
}
```

**Table 73 — Machine Status Monitoring Settings Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| enableStatusDetails | Bool | Status details. `true` = enabled. |
| enableAdjustedManualStatus | Bool | Setup-mode status correction. `true` = enabled. |
| maxManualPauseTime | Int32 | Maximum setup-mode pause time [seconds]. |
| enableStatusConversion | Bool | Status conversion. `true` = enabled. |
| statusConversionSettings | String | Status conversion rule. Not returned if not configured. |

---

### 2.9.5.16 update-machine-status-monitoring-settings — Modify Machine Status Monitoring Settings

Modifies machine status monitoring settings and returns the updated settings.

```
POST  /api/config/update-machine-status-monitoring-settings
```

**Request Body Example**

```json
{
  "enableStatusDetails": true,
  "enableAdjustedManualStatus": true,
  "maxManualPauseTime": 300,
  "enableStatusConversion": true,
  "statusConversionSettings": "CNCStatus_cncStatus = \"MANUAL_MDI_RUN\" | AUTO_RUN"
}
```

Request/response parameters: see Table 73.

---

## 2.9.6 Communication Configuration Interfaces

The communication configuration interfaces are used to get or modify communication settings. For background information, refer to section 5.6 Communication Configuration in the product manual.

### 2.9.6.1 cloud-settings — Get Cloud Platform Settings

This interface has no request parameters.

```
GET  /api/config/cloud-settings
```

**Response Example**

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

**Table 74 — Cloud Platform Settings Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| enable | Bool | Enable cloud platform. `true` = enabled. |
| serverAddress | String | Server address. Not returned if not configured. |
| token | String | Gateway token. Not returned if not configured. |
| enableBroadcasting | Bool | Enable broadcasting. `true` = enabled. When enabled, data is broadcast through the cloud platform. |
| enableGatewayLinking | Bool | Enable gateway linking. `true` = enabled. When enabled, receives data broadcast by linked gateways via the cloud platform. |
| gatewayLinks | String | Linked gateways. Not returned if not configured. |

---

### 2.9.6.2 update-cloud-settings — Modify Cloud Platform Settings

Modifies cloud platform settings and returns the updated settings.

```
POST  /api/config/update-cloud-settings
```

Request/response parameters: see Table 74.

---

### 2.9.6.3 modbus-settings — Get MODBUS Settings

This interface has no request parameters.

```
GET  /api/config/modbus-settings
```

**Response Example**

```json
{
  "enable": false,
  "byte4Order": "DCBA",
  "byte8Order": "HGFEDCBA",
  "encoding": "GBK",
  "reverseString": false
}
```

**Table 75 — MODBUS Settings Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| enable | Bool | Enable MODBUS. `true` = enabled. |
| byte4Order | String | 32-bit type byte order. Supported values: `ABCD`, `BADC`, `CDAB`, `DCBA`, etc. |
| byte8Order | String | 64-bit type byte order. Supported values: `ABCDEFGH`, `BADCFEHG`, `GHEFCDAB`, `HGFEDCBA`, etc. |
| encoding | String | Character encoding (e.g., `ASCII`, `UTF-8`, `GBK`). |
| reverseString | Bool | Reverse string. `true` = enabled. |

---

### 2.9.6.4 update-modbus-settings — Modify MODBUS Settings

Modifies MODBUS settings and returns the updated settings.

```
POST  /api/config/update-modbus-settings
```

Request/response parameters: see Table 75.

---

### 2.9.6.5 mqtt-settings — Get MQTT Settings

This interface has no request parameters.

```
GET  /api/config/mqtt-settings
```

**Response Example**

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

**Table 76 — MQTT Settings Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| enable | Bool | Enable MQTT. `true` = enabled. |
| mode | String | Mode. See Table 77 — MQTT mode Values. |
| brokerAddress | String | Broker server address. |
| port | Int32 | Broker server port. |
| clientID | String | Client ID. |
| username | String | Username. |
| password | String | Password. |
| dataReportTopic | String | Data reporting topic. |
| rpcRequestTopic | String | RPC request topic. |
| rpcResponseTopic | String | RPC response topic. |
| encoding | String | Character encoding (e.g., `ASCII`, `UTF-8`, `GBK`). |
| allowAnomynous | Bool | Anonymous login. `true` = enabled. |
| arrayToString | Bool | Convert arrays to string. `true` = enabled. |
| publishOnValueChange | Bool | Publish on value change. `true` = enabled. Default: `false`. |
| publishAllOnValueChange | Bool | Publish all fields on value change. `true` = enabled. Applies when value-change publishing is enabled. Default: `false` — only the changed fields are published. |
| timeoutReportingInterval | Int32 | Timeout reporting interval [seconds]. Applies when value-change publishing is enabled. Default: `0` (timeout reporting disabled). |

**Table 77 — MQTT mode Values**

| Option |
|--------|
| Default |
| TB2 |
| Brm |
| IoTDA |
| WisIoT |
| MKT |
| TB |

---

### 2.9.6.6 update-mqtt-settings — Modify MQTT Settings

Modifies MQTT settings and returns the updated settings.

```
POST  /api/config/update-mqtt-settings
```

Request/response parameters: see Table 76.

---

### 2.9.6.7 database-settings — Get Database Settings

This interface has no request parameters.

```
GET  /api/config/database-settings
```

**Response Example** (InfluxDB v2.x)

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

**Response Example** (non-InfluxDB v2.x)

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
  "loggingModeTypes": ["AlarmHistory", "AxialOverload", "CNCStatus", "Count"],
  "realTimeModeTypes": ["AlarmLog", "GroupOEE", "OEE"]
}
```

**Table 78 — Database Settings Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| enable | Bool | Enable database. `true` = enabled. |
| type | String | Database type. See Table 79 — Database Types. |
| serverAddress | String | Server address. |
| port | String | Server port. |
| username | String | Username. |
| password | String | Password. |
| bucket | String | Database bucket. InfluxDB v2.x only. |
| organization | String | Organization name. InfluxDB v2.x only. |
| database | String | Database name. Non-InfluxDB v2.x only. |
| storageMode | String | Storage mode. Non-InfluxDB v2.x only. See Table 80 — Database Storage Modes. |
| dataRetentionPeriod | Int32 | Data retention period [hours]. Non-InfluxDB v2.x only. |
| loggingModeTypes | String[] | Effective when `storageMode` is `User Defined`. See section 1.2 for data type names. |
| noActionModeTypes | String[] | Effective when `storageMode` is `User Defined`. See section 1.2 for data type names. |
| realTimeModeTypes | String[] | Effective when `storageMode` is `User Defined`. See section 1.2 for data type names. |
| useLocalTime | Bool | Use local time. Non-InfluxDB v2.x types only. |
| enablePrimaryKey | Bool | Enable primary key. Non-InfluxDB v2.x types only. |
| tablePrefix | String | Table name prefix. |
| writeOnValueChange | Bool | Write on value change. `true` = enabled. |
| timeoutReportingInterval | Int32 | Timeout reporting interval [seconds]. Applies when value-change writing is enabled. Default: `0` (timeout writing disabled). |

**Table 79 — Database Types**

| Option |
|--------|
| InfluxDB v2.x |
| MySQL |
| SQL Server |
| Postgre SQL |

**Table 80 — Database Storage Modes**

| Option | Description |
|--------|-------------|
| Logging | Log mode (write on change) |
| Real Time | Real-time mode (write on every collection) |
| User Defined | Custom mode |

---

### 2.9.6.8 update-database-settings — Modify Database Settings

Modifies database settings and returns the updated settings.

```
POST  /api/config/update-database-settings
```

Request/response parameters: see Table 78.

---

### 2.9.6.9 gateway-file-server-settings — Get Gateway File Server Settings

This interface has no request parameters.

```
GET  /api/config/gateway-file-server-settings
```

**Response Example**

```json
{
  "enable": false,
  "enableFtp": false,
  "enableSmb": false,
  "username": "dncuser",
  "password": "dncuser"
}
```

**Table 81 — Gateway File Server Settings Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| enable | Bool | Enable gateway file server. `true` = enabled. |
| enableFtp | Bool | Enable FTP. `true` = enabled. |
| enableSmb | Bool | Enable shared folder (SMB). `true` = enabled. |
| username | String | Username. Read-only; cannot be modified. |
| password | String | Password. |

---

### 2.9.6.10 update-gateway-file-server-settings — Modify Gateway File Server Settings

Modifies gateway file server settings and returns the updated settings.

```
POST  /api/config/update-gateway-file-server-settings
```

Request/response parameters: see Table 81.

---

### 2.9.6.11 http-settings — Get HTTP Settings

This interface has no request parameters.

```
GET  /api/config/http-settings
```

**Response Example**

```json
{
  "enable": true,
  "enableIpWhiteList": true,
  "ipWhiteList": "192.168.100.200;192.168.100.201;"
}
```

**Table 82 — HTTP Settings Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| enable | Bool | Enable HTTP. `true` = enabled. |
| enableIpWhiteList | Bool | Enable IP whitelist. `true` = enabled. |
| ipWhiteList | String | IP whitelist (semicolon-separated). Not returned if not configured. |

---

### 2.9.6.12 update-http-settings — Modify HTTP Settings

Modifies HTTP settings and returns the updated settings.

```
POST  /api/config/update-http-settings
```

Request/response parameters: see Table 82.

---

### 2.9.6.13 hub-settings — Get Hub Settings

This interface has no request parameters.

```
GET  /api/config/hub-settings
```

**Response Example**

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

**Table 83 — Hub Settings Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| enable | Bool | Enable Hub. `true` = enabled. |
| server | String | Server address. Not returned if not configured. |
| accessToken | String | Gateway token. Not returned if not configured. |
| enableBroadcasting | Bool | Enable broadcasting. `true` = enabled. When enabled, data is broadcast through the Hub. |
| enableGatewayLinking | Bool | Enable gateway linking. `true` = enabled. When enabled, receives data broadcast by linked gateways via the Hub. |
| gatewayLinks | String | Linked gateways. Not returned if not configured. |

---

### 2.9.6.14 update-hub-settings — Modify Hub Settings

Modifies Hub settings and returns the updated settings.

```
POST  /api/config/update-hub-settings
```

Request/response parameters: see Table 83.

---

### 2.9.6.15 remote-access — Get Remote Access Settings

This interface has no request parameters.

```
GET  /api/config/remote-access
```

**Response Example**

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

**Table 84 — Remote Access Settings Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| host | String | Remote server address. |
| hub | String | Site (read-only). Defaults to the gateway UID. |
| password | String | Password. |
| bridgeNetwork | String | Bridged network: `"LAN1"`, `"LAN2"`, or `""` (no bridge). |
| state | String | Connection state (read-only). Values: `Connecting`, `Negotiation`, `Retry` (all three indicate connecting), `Auth` (authenticating), `Established` (connected), `Idle` (disconnected). |
| expiration | String | Expiration date (read-only). Only returned when `state` is `Established`. |

---

### 2.9.6.16 update-remote-access — Modify Remote Access Settings

Modifies remote access settings and returns the updated settings.

```
POST  /api/config/update-remote-access
```

**Request Body Example**

```json
{
  "host": "serverUrl2",
  "password": "password2",
  "bridgeNetwork": "LAN1"
}
```

Request/response parameters: see Table 84.
