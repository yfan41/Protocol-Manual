# Chapter II — HTTP Communication (Continued)

## 2.5 Data Read/Write Interfaces

The data read/write interfaces allow reading machine data or machine group data through the gateway, and writing data to machines. These interfaces are divided into two categories: **Direct Read/Write** and **Cached Read**.

---

### 2.5.1 Direct Read/Write

When using direct read/write interfaces, the gateway immediately communicates with the target machine to read or write data.

---

### 2.5.1.1 readAlarm — Read Alarm Information

```
GET  /api/cnc/readAlarm?machineID=MACHINEID
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |

**Response Example**

```json
{
  "alarmMsg": [
    "Alarm 1",
    "Alarm 2"
  ],
  "alarmLevel": [
    "WRN",
    "WRN"
  ]
}
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| alarmMsg | String[] | (Required) Current alarm messages on the machine. Returns an empty array if no alarms are active. |
| alarmLevel | String[] | (Required) Alarm levels, in the same order as `alarmMsg`. Returns an empty array if no alarms. See Table 48 — Alarm Level. |

> Note: Starting from v1.8.1, alarm timestamps have been removed. To obtain alarm start times, use section 2.7.1.2 alarm — Machine Alarm Data Analysis.

---

### 2.5.1.2 readCNCStatus — Read Machine Status

```
GET  /api/cnc/readCNCStatus?machineID=MACHINEID&channel=CHANNEL
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| channel | Int32 | Channel number. If not specified, defaults to `0` (main channel). |

**Response Example**

```json
{
  "cncStatus": "MANUAL_EDIT",
  "alarmStatus": "ALARM",
  "alarmLevel": "WRN",
  "channel": 2
}
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| cncStatus | String | (Required) Running status. See Table 44 — CNC Running Status. |
| alarmStatus | String | (Required) Alarm status. See Table 45 — Alarm Status. |
| alarmLevel | String | Alarm level. If there are multiple alarms, the highest level is returned. Not returned if no alarms. See Table 48 — Alarm Level. |
| channel | Int32 | Channel number. Only returned when `channel` is specified in the request and is not `0`. |

> Note: The adjusted running status `adjustedStatus`, cumulative off time `offTime`, cumulative wait time `waitTime`, cumulative emergency stop time `emergencyTime`, cumulative auto run time `autoRunTime`, and cumulative setup time `ManualTime` — computed by gateway post-processing — are not currently supported over HTTP. They can be obtained after enabling the auto-collection tasks, via sections 2.5.2.1 `readTaskData` or 2.5.2.2 `batchReadTaskData`, or via MODBUS, MQTT, or database interfaces.

---

### 2.5.1.3 readCNCStatusDetails — Read Machine Status Details

In addition to the same general machine status data as section 2.5.1.2 `readCNCStatus`, this interface also returns non-generic raw status data.

```
GET  /api/cnc/readCNCStatusDetails?machineID=MACHINEID&channel=CHANNEL
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| channel | Int32 | Channel number. If not specified, defaults to `0` (main channel). |

**Response Example**

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

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| mode | String | Operating mode |
| programStatus | String | Program status |
| emergencyStatus | String | Emergency stop status. See Table 46 — Emergency Stop Status. |
| dryRunStatus | String | Dry run status |
| cncStatus | String | (Required) Running status. See Table 44 — CNC Running Status. |
| alarmStatus | String | (Required) Alarm status. See Table 45 — Alarm Status. |
| alarmLevel | String | Alarm level. Highest level when multiple alarms exist. Not returned if no alarms. See Table 48 — Alarm Level. |
| channel | Int32 | Channel number. Only returned when `channel` is specified and not `0`. |

---

### 2.5.1.4 readCount — Read Machining Count

```
GET  /api/cnc/readCount?machineID=MACHINEID
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |

**Response Example**

```json
{
  "count": 1052
}
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| count | Int32 | (Required) Machining count retrieved from the CNC system |

> Note: The gateway-derived cumulative machining count `cumulativeCount` and the current monitoring period count `currentCount` are not currently supported over HTTP. They can be obtained after enabling auto-collection tasks, via sections 2.5.2.1 `readTaskData` or 2.5.2.2 `batchReadTaskData`, or via MODBUS, MQTT, or database interfaces.

---

### 2.5.1.5 readCurrentToolNumber — Read Current Tool Number

```
GET  /api/cnc/readCurrentToolNumber?machineID=MACHINEID&channel=CHANNEL
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| channel | Int32 | Channel number. If not specified, defaults to `0` (main channel). |

**Response Example**

```json
{
  "toolNumber": "6",
  "toolOffsetNum": "6",
  "channel": 2
}
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| toolNumber | String | (Required) Current tool number |
| toolOffsetNum | String | Current tool offset number |
| channel | Int32 | Channel number. Only returned when `channel` is specified and not `0`. |

---

### 2.5.1.6 readEnergyConsum — Read Energy Consumption Data

Currently supported for selected Brother machines and simulated machines.

```
GET  /api/cnc/readEnergyConsum?machineID=MACHINEID
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |

**Response Example**

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

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| allServoAxes | Double | Total energy consumption for all servo axes [Wh] |
| coolantPump | Double | Coolant pump energy consumption [Wh] |
| ctsPump | Double | CTS (through-spindle coolant) pump energy consumption [Wh] |
| chipFlusherPump | Double | Chip flusher pump energy consumption [Wh] |
| cyclonePump | Double | Cyclone filter pump energy consumption [Wh] |
| system24V | Double | 24 V system energy consumption [Wh] |
| ncControl | Double | NC control energy consumption [Wh] |
| lcdBacklight | Double | LCD backlight energy consumption [Wh] |
| chipConveyor | Double | Chip conveyor energy consumption [Wh] |
| screwConveyor | Double | Screw conveyor energy consumption [Wh] |
| autoLubrication | Double | Auto lubrication unit energy consumption [Wh] |
| spindleCoolingFan | Double | Spindle cooling fan energy consumption [Wh] |
| servoControl | Double | Servo control energy consumption [Wh] |

---

### 2.5.1.7 readFeed — Read Feed Data

Laser cutting / welding machines only.

```
GET  /api/cnc/readFeed?machineID=MACHINEID&channel=CHANNEL
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| channel | Int32 | Channel number. If not specified, defaults to `0` (main channel). |

**Response Example**

```json
{
  "overFeed": 100.0,
  "actFeed": 80.0,
  "overRapid": 100.0,
  "channel": 2
}
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| overFeed | Float | Feed override [%] |
| actFeed | Float | Actual feed rate |
| overRapid | Float | Rapid traverse override [%] |
| channel | Int32 | Channel number. Only returned when `channel` is specified and not `0`. |

---

### 2.5.1.8 readFeedAndSpindle — Read Feed and Spindle Data

CNC machines only.

```
GET  /api/cnc/readFeedAndSpindle?machineID=MACHINEID&channel=CHANNEL
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| channel | Int32 | Channel number. If not specified, defaults to `0` (main channel). |

**Response Example**

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

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| overSpindle | Float | Spindle speed override [%] |
| actSpindle | Float | Actual spindle speed |
| overFeed | Float | Feed override [%] |
| actFeed | Float | Actual feed rate |
| overRapid | Float | Rapid traverse override [%] |
| channel | Int32 | Channel number. Only returned when `channel` is specified and not `0`. |

> Note: When multiple spindles are present, only the first spindle speed is returned.

---

### 2.5.1.9 readLaserPower — Read Laser Power

Laser cutting / welding machines only.

```
GET  /api/cnc/readLaserPower?machineID=MACHINEID
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |

**Response Example**

```json
{
  "preset": 100.0,
  "actual": 100.0
}
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| preset | Float | Preset power [%] |
| actual | Float | Actual power [%] |

---

### 2.5.1.10 readLoad — Read Load Data

CNC machines only.

```
GET  /api/cnc/readLoad?machineID=MACHINEID&channel=CHANNEL
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| channel | Int32 | Channel number. If not specified, defaults to `0` (main channel). |

**Response Example**

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

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| spindleLoad | Float[] | Spindle load [%] |
| axialLoad | Float[] | Servo axis load [%] |
| channel | Int32 | Channel number. Only returned when `channel` is specified and not `0`. |

> Note: When multiple spindles are present, the spindle load array entries correspond to the first spindle, second spindle, etc. in order. The servo axis load entries correspond in order to the axis names returned by section 2.5.1.15 `readPosition`.

---

### 2.5.1.11 readLog — Read Log Information

Currently supports Fanuc Robot, ABB Robot, and simulated machines.

```
GET  /api/cnc/readLog?machineID=MACHINEID
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| type | String | Log type. Values: `Alarm` (alarm log), `Operation` (operation log), `Default`. Defaults to `Default`. ABB Robot supports `Operation`; Fanuc Robot supports `Alarm`; simulated machines support all types; `Default` is equivalent to `Operation`. |
| count | Int32 | Number of log entries. Returns the specified number of the most recent entries. Defaults to `0` (all entries). |

**Response Example** (ABB Robot — Operation)

```json
{
  "msgs": [
    "{\"SeqNo\":\"79\",\"Type\":\"1 \",\"Code\":\"10011\",\"Title\":\"Motors ON state\",\"Date\":\"2025-04-02 T 10:54:40\",\"Description\":\"The system is in the Motors ON state.\",\"Args\":[]}",
    "{\"SeqNo\":\"78\",\"Type\":\"1 \",\"Code\":\"10010\",\"Title\":\"Motors OFF state\",\"Date\":\"2025-04-02 T 10:54:39\",\"Description\":\"The system is in the Motors OFF state.\",\"Args\":[]}"
  ]
}
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| msgs | String[] | (Required) Log messages. Note: the format of log content varies by machine type. |

---

### 2.5.1.12 readOffsetData — Read Tool Offset Data

```
GET  /api/cnc/readOffsetData?machineID=MACHINEID&channel=CHANNEL&toolNum=TOOLNUM&offsetNum=OFFSETNUM
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| channel | Int32 | Channel number. If not specified, defaults to `0` (main channel). |
| toolNum | Int32 | Specify `toolNum` (tool number) and/or `offsetNum` (offset number) to identify the target tool. Parameters correspond to the tags in Table 26 — Offset Tags. Refer to Table 27 — Offset Tag Combinations for the tag combination supported by each CNC system. |
| offsetNum | Int32 | See above. |

**Response Example** (Heidenhain)

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

**Response Example** (Siemens)

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

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| channel | Int32 | Channel number. Only returned when `channel` is specified and not `0`. |
| toolNum | Int32 | Tool number |
| offsetNum | Int32 | Offset number |
| toolName | String | Tool name |
| toolType | String | Tool type |
| lengthUnit | String | Tool offset length unit |
| toolNose | Int32 | Tool nose / cutting edge position |
| lengthWear | Double | Length wear offset |
| radiusWear | Double | Radius wear offset |
| lengthGeom | Double | Length geometry offset |
| radiusGeom | Double | Radius geometry offset |
| wearX | Double | Length X wear offset |
| wearY | Double | Length Y wear offset |
| wearZ | Double | Length Z wear offset |
| wearR | Double | Radius wear offset |
| geomX | Double | Length X geometry |
| geomY | Double | Length Y geometry |
| geomZ | Double | Length Z geometry |
| geomR | Double | Radius geometry |

> Note: The parameters listed above are the common tool offset parameters, but are not exhaustive. It is recommended to test which offset parameters can be read when connecting a new CNC type for the first time. If you need access to tool offset parameters not currently included in this interface, contact support.

---

### 2.5.1.13 batchReadOffsetData — Batch Read Tool Offset Data

This interface is the batch version of section 2.5.1.12 `readOffsetData`. By specifying a list of target tools in the request body, multiple sets of data can be read in a single call.

```
POST  /api/cnc/batchReadOffsetData?machineID=MACHINEID
```

**Request Body Example** (application/json)

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

**Request Parameters** (same as section 2.5.1.12 `readOffsetData`)

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| channel | Int32 | Channel number. If not specified, defaults to `0` (main channel). |
| toolNum | Int32 | Specify tool number or offset number to identify the target tool |
| offsetNum | Int32 | See above. |

**Response Example**

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

Response parameters are the same as section 2.5.1.12 `readOffsetData`. If a particular request fails, the corresponding position in the response array contains the error information for that request.

---

### 2.5.1.14 writeOffsetData — Write Tool Offset Data

```
POST  /api/cnc/writeOffsetData?machineID=MACHINEID&channel=CHANNEL&toolNum=TOOLNUM&offsetNum=OFFSETNUM
```

**Request Body Example** (application/json)

```json
{
  "toolNose": 3,
  "lengthWear": 0.0001,
  "radiusWear": 0.03,
  "lengthGeom": 5.0,
  "radiusGeom": 0.2
}
```

> Note: Only include in the request body the parameters you need to write. Parameters that should remain unchanged do not need to be specified.

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| channel | Int32 | Channel number. If not specified, defaults to `0` (main channel). |
| toolNum | Int32 | Specify tool number or offset number to identify the target tool |
| offsetNum | Int32 | See above. |
| toolName | String | Tool name. (Read-only in Siemens systems; cannot be written.) |
| toolType | String | Tool type. (Read-only in Okuma systems; cannot be written.) |
| lengthUnit | String | Tool offset length unit |
| toolNose | Int32 | Tool nose / cutting edge position |
| lengthWear | Double | Length wear offset |
| radiusWear | Double | Radius wear offset |
| lengthGeom | Double | Length geometry offset |
| radiusGeom | Double | Radius geometry offset |
| wearX | Double | Length X wear offset |
| wearY | Double | Length Y wear offset |
| wearZ | Double | Length Z wear offset |
| wearR | Double | Radius wear offset |
| geomX | Double | Length X geometry |
| geomY | Double | Length Y geometry |
| geomZ | Double | Length Z geometry |
| geomR | Double | Radius geometry |

**Response Example**

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| errorCode | Int32 | (Required) Error code. `0` = success. |
| errorMsg | String | (Required) Error message |

---

### 2.5.1.15 readPosition — Read Axis Position Data

```
GET  /api/cnc/readPosition?machineID=MACHINEID&channel=CHANNEL
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| channel | Int32 | Channel number. If not specified, defaults to `0` (main channel). |

**Response Example**

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

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| axisName | String[] | (Required) Axis names |
| unit | String[] | Units for each axis |
| mach | Float[] | Machine position for each axis |
| abs | Float[] | Absolute position for each axis |
| rel | Float[] | Relative position for each axis |
| dist | Float[] | Distance to go for each axis |
| channel | Int32 | Channel number. Only returned when `channel` is specified and not `0`. |

> Note: All position data arrays correspond positionally to the axis names in `axisName`.

---

### 2.5.1.16 readProgramBlock — Read Current Program Block

Reads the program block currently executing on the machine.

```
GET  /api/cnc/readProgramBlock?machineID=MACHINEID&channel=CHANNEL
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| channel | Int32 | Channel number. If not specified, defaults to `0` (main channel). |

**Response Example**

```json
{
  "currentBlock": "Y70.800",
  "channel": 2
}
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| currentBlock | String | (Required) Currently executing program block content |
| channel | Int32 | Channel number. Only returned when `channel` is specified and not `0`. |

---

### 2.5.1.17 readProgramInfo — Read Program Information

```
GET  /api/cnc/readProgramInfo?machineID=MACHINEID&channel=CHANNEL
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| channel | Int32 | Channel number. If not specified, defaults to `0` (main channel). |

**Response Example**

```json
{
  "mainPrgmName": "1234",
  "runningPrgName": "abcd",
  "programSeqNum": "N30",
  "programStack": "A/B/1234/abcd",
  "channel": 2
}
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| mainPrgmName | String | (Required) Main program name |
| runningPrgName | String | (Required) Currently executing sub-program name |
| programSeqNum | String | Program sequence number of the currently executing block |
| programStack | String | Program call stack. Currently supported only for Siemens and simulated machines. |
| channel | Int32 | Channel number. Only returned when `channel` is specified and not `0`. |

> Note: For Fanuc systems, leading zeros in standard program names (letter O + digits) are automatically removed. For example, if the program name on the machine is `O0010`, the returned name is `010`.

---

### 2.5.1.18 readPlcData — Read PLC Data

Before using this interface, you must know the PLC area address and data type of the target data. Consult the device manual or contact the equipment manufacturer for this information.

```
GET  /api/cnc/readPlcData?machineID=MACHINEID&area=AREA&start=START&count=COUNT&type=TYPE
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| area | String | (Required) Area. Format: `{areaName}\|{parameter1}\|{parameter2}…`. `areaName` is the PLC area (e.g., R, X, Y — obtain from the machine manual or manufacturer). For standard MODBUS communication, `areaName` is a partition name such as `0x`, `1x`, `3x`, `4x`. The gateway supports special area names including `$MACRO$` for macro variables, `$PARAMS$` for system parameters, and `$TAG$` for tags. See Table 52 — PLC Special Areas and Parameters. |
| start | String | Start address. If not base-10, add a prefix per Table 53 — PLC Address Numeral System Prefixes. |
| count | Int32 | (Required) Data count. For non-String types, this is the number of data items to read; for String type, this is the target string length in bytes. |
| type | String | (Required) Data type. Supported types are listed in Table 54 — PLC Data Types. |
| channel | Int32 | Channel number. If not specified, defaults to `0` (main channel). |

**Table 51 — PLC Common Areas and Parameters**

| Device System | Area | `area` Example | Notes |
|--------------|------|---------------|-------|
| All | General area | X | X area of the PLC device |
| All | General area | X\|s=S | To communicate with another PLC connected to the current PLC, append `station`, `slot`, or `slaveID` (MODBUS). If `s` is not specified, defaults to communicating with the current PLC. Currently supported by select PLC devices only. |

**Table 52 — PLC Special Areas and Parameters (Partial)**

| System | Area | `area` Example | Notes |
|--------|------|---------------|-------|
| Bosunman | MODBUS address | `0x` or `1x` or `4x` | |
| Brother | Macro variables | `$MACRO$` | |
| Delta | Macro variables | `$MACRO$` | |
| Fanuc | Diagnostic data | `$DIAG$` | |
| Fanuc | Macro variables | `$MACRO$` | |
| Fanuc | System parameters | `$PARAMS$` | Supports Byte, Int16, Int32, Double; some parameters require an axis number, e.g., `axis=1` |
| GSK [980, 988] | Macro variables | `$MACRO$` | |
| Haas [Universal] | Macro variables | `$MACRO$` | |
| Siemens | Macro variables (R parameters) | `$MACRO$\|type=R` | |
| Siemens | Macro variables (RG parameters) | `$MACRO$\|type=RG` | |
| Siemens [Universal] | Tags (variable names) | `$TAG$\|area=AREA\|block=BLOCK\|name=NAME\|areaNo=AREANO\|row=ROW\|column=COLUMN` | S7 variables; supply Area, Block (Component), VariableName, AreaNo. (default 1), Row (default 1), Column (default 1) |

**Table 53 — PLC Address Numeral System Prefixes**

| Base | Prefix (digit 0 + letter) | Example |
|------|--------------------------|---------|
| Binary | 0b | 0b1001 |
| Octal | 0 | 03671 |
| Decimal | (none) | 123 |
| Hexadecimal | 0x | 0x5A7 |

**Table 54 — PLC Data Types (Support Varies by System)**

| Data Type | Bit | Byte | SByte | Int16 | UInt16 | Float | Int32 | UInt32 | Double | String |
|-----------|-----|------|-------|-------|--------|-------|-------|--------|--------|--------|
| Bosunman | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | |
| Brother | ✓ | | | ✓ | | | | | | |
| Delta | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Fanuc | | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | | * | |
| Siemens | ✓ | ✓ | | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Mock | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |

✓: Supported; *: Only supported for macro variables (local variables, common variables, etc.)

**Response Example** (general area, Int32 array)

```json
{
  "data": [2147483647, -616240881, 1351548766, 1408080714, -1677592641, 681965706, 1002992212, -1588442413]
}
```

**Response Example** (String type)

```json
{
  "msg": "Test string"
}
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| data | Array\[Object\] | When `type` is any type other than String, the response contains `data` — an array of the specified `type`, with length equal to the requested `count`. |
| msg | String | When `type` is String, the response contains `msg`. Trailing whitespace is automatically trimmed. |
| channel | Int32 | Channel number. Only returned when `channel` is specified and not `0`. |

---

### 2.5.1.19 writePlcData — Write PLC Data

> **Warning**: Writing PLC data carries inherent risks. Ensure you fully understand the risks and only write data in safe conditions. By default, this interface is disabled on the gateway. It must be explicitly enabled in the **Settings** page of the gateway management interface before use.

Before using this interface, you must know the PLC area address and data type of the target data.

```
POST  /api/cnc/writePlcData?machineID=MACHINEID&area=AREA&start=START&type=TYPE
```

**Request Body Example** (writing non-String type, application/json)

```json
{
  "data": [2147483647, -616240881, 1351548766, 1408080714]
}
```

**Request Body Example** (writing String type)

```json
{
  "msg": "Test string"
}
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| area | String | (Required) Area. Format is the same as the `area` parameter in `readPlcData`. |
| start | String | Start address. Add a prefix per Table 53 if not base-10. |
| type | String | (Required) Data type. See Table 54 — PLC Data Types. |
| data | Array\[Object\] | For non-String types, provide the data array in the request body. |
| msg | String | For String type, provide the string value in the request body. |
| channel | Int32 | Channel number. If not specified, defaults to `0` (main channel). |

**Response Example**

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| errorCode | Int32 | (Required) Error code. `0` = success. |
| errorMsg | String | (Required) Error message |

---

### 2.5.1.20 readTimeData — Read Machine Time Data

```
GET  /api/cnc/readTimeData?machineID=MACHINEID
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |

**Response Example**

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

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| cumulativePowerOnTimeMin | Int32 | Cumulative power-on time 1 [minutes] |
| cumulativePowerOnTimeMs | Int32 | Cumulative power-on time 2 [milliseconds] |
| powerOnTimeMin | Int32 | Current power-on time 1 [minutes] |
| powerOnTimeMs | Int32 | Current power-on time 2 [milliseconds] |
| cumulativeOperatingTimeMin | Int32 | Cumulative operating time 1 [minutes] |
| cumulativeOperatingTimeMs | Int32 | Cumulative operating time 2 [milliseconds] |
| operatingTimeMin | Int32 | Current operating time 1 [minutes] |
| operatingTimeMs | Int32 | Current operating time 2 [milliseconds] |
| cumulativeCuttingTimeMin | Int32 | Cumulative cutting time 1 [minutes] |
| cumulativeCuttingTimeMs | Int32 | Cumulative cutting time 2 [milliseconds] |
| cuttingTimeMin | Int32 | Current cutting time 1 [minutes] |
| cuttingTimeMs | Int32 | Current cutting time 2 [milliseconds] |
| lastCycleTimeMin | Int32 | Last cycle time 1 [minutes] |
| lastCycleTimeMs | Int32 | Last cycle time 2 [milliseconds] |
| currentCycleTimeMin | Int32 | Current cycle elapsed time 1 [minutes] |
| currentCycleTimeMs | Int32 | Current cycle elapsed time 2 [milliseconds] |
| currentCuttingTimeMin | Int32 | Current cutting time (within current cycle) 1 [minutes] |
| currentCuttingTimeMs | Int32 | Current cutting time (within current cycle) 2 [milliseconds] |
| remainingCycleTimeMin | Int32 | Remaining cycle time 1 [minutes] |
| remainingCycleTimeMs | Int32 | Remaining cycle time 2 [milliseconds] |

> Note: The interface only returns time data supported by the CNC system. Each time value is derived by combining its corresponding time 1 and time 2 through addition. For the calculation method, see section 1.2.23 TimeData — Machine Time Data.

---

### 2.5.1.21 readToolLife — Read Tool Life Data

```
GET  /api/cnc/readToolLife?machineID=MACHINEID&groupNum=GROUPNUM&toolIndex=TOOLINDEX&toolNum=TOOLNUM&offsetNum=OFFSETNUM
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| groupNum | Int32 | Specify one or more of: `groupNum` (tool group number), `toolIndex` (tool index within group), `toolNum` (tool number), or `offsetNum` (offset number) to identify the target tool. |
| toolIndex | Int32 | See above. |
| toolNum | Int32 | Parameters correspond to the tags in section 1.2.25 ToolLife. Note: `groupNum` corresponds to tag `toolGroupNum`; `offsetNum` corresponds to tag `toolOffsetNum`; others match tag names directly. Refer to the tag combination tables in section 1.2.25 for your CNC system. |
| offsetNum | Int32 | See above. |

**Response Example** (Fanuc — count-based)

```json
{
  "toolNum": 32,
  "toolIndex": 4,
  "toolGroupNum": 1,
  "countLimit": 100,
  "currentCount": 21
}
```

**Response Example** (Fanuc — time-based)

```json
{
  "toolNum": 8,
  "toolIndex": 2,
  "toolGroupNum": 1,
  "timeLimit": 60000,
  "currentTime": 1380
}
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| toolGroupNum | Int32 | Tool group number |
| toolIndex | Int32 | Tool index within the group |
| toolNum | Int32 | Tool number |
| toolOffsetNum | Int32 | Tool offset number |
| timeLimit | Int32 | Tool life time limit [seconds] — maximum usable time |
| currentTime | Int32 | Current tool life used [seconds] |
| prewarningTime | Int32 | Tool life pre-warning threshold [seconds] — alarm triggered when `currentTime` reaches this value |
| countLimit | Int32 | Tool life count limit — maximum usable count |
| currentCount | Int32 | Current tool life used count |
| prewarningCount | Int32 | Tool life pre-warning count threshold |
| wearLimit | Int32 | Tool life wear limit [machine default length unit] |
| currentWear | Int32 | Current tool life wear [machine default length unit] |
| prewarningWear | Int32 | Tool life pre-warning wear threshold [machine default length unit] |

> Note: The parameters listed are standardized tool life data; some values are converted from raw data for uniformity across CNC systems. To access raw data, use section 2.5.1.23 `readToolLifeDetails`. Supported life types per CNC system are listed in section 1.2.25 ToolLife.

---

### 2.5.1.22 batchReadToolLife — Batch Read Tool Life Data

This interface is the batch version of section 2.5.1.21 `readToolLife`. By specifying a list of target tools in the request body, multiple sets of data can be read in a single call.

```
POST  /api/cnc/batchReadToolLife?machineID=MACHINEID
```

**Request Body Example** (application/json)

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

**Request Parameters** (same as section 2.5.1.21 `readToolLife`)

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| groupNum | Int32 | Specify one or more of `groupNum`, `toolIndex`, `toolNum`, or `offsetNum` to identify the target tool |
| toolIndex | Int32 | See above. |
| toolNum | Int32 | See above. |
| offsetNum | Int32 | See above. |

**Response Example**

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

Response parameters are the same as section 2.5.1.21 `readToolLife`. If a particular request fails, the corresponding position in the response array contains that request's error information.

---

### 2.5.1.23 readToolLifeDetails — Read Tool Life Details

In addition to the common tool life data available from section 2.5.1.21 `readToolLife`, this interface also returns unprocessed raw data and additional non-generic tool life parameters.

```
GET  /api/cnc/readToolLifeDetails?machineID=MACHINEID&groupNum=GROUPNUM&toolIndex=TOOLINDEX&toolNum=TOOLNUM&offsetNum=OFFSETNUM
```

**Request Parameters** (same as section 2.5.1.21 `readToolLife`)

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| groupNum | Int32 | Specify one or more of `groupNum`, `toolIndex`, `toolNum`, or `offsetNum` to identify the target tool |
| toolIndex | Int32 | See above. |
| toolNum | Int32 | See above. |
| offsetNum | Int32 | See above. |

**Response Example** (Fanuc — count-based)

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

**Response Example** (Fanuc — time-based)

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

**Response Example** (Siemens — time-based)

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

**Response Example** (Siemens — wear-based)

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

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| **Common Data** | | |
| groupNum | Int32 | Tool group number |
| toolIndex | Int32 | Tool index within the group |
| toolNum | Int32 | Tool number |
| toolOffsetNum | Int32 | Tool offset number |
| timeLimit | Int32 | Tool life time limit [seconds] |
| currentTime | Int32 | Current tool life used [seconds] |
| prewarningTime | Int32 | Tool life pre-warning threshold [seconds] |
| countLimit | Int32 | Tool life count limit |
| currentCount | Int32 | Current tool life used count |
| prewarningCount | Int32 | Tool life pre-warning count threshold |
| wearLimit | Int32 | Tool life wear limit [machine default length unit] |
| currentWear | Int32 | Current tool life wear [machine default length unit] |
| prewarningWear | Int32 | Tool life pre-warning wear threshold [machine default length unit] |
| **Raw Data** | | |
| rawToolLifeType | String | Tool life type |
| rawToolLifeUnit | String | Time unit (referred to as [time] below) |
| rawToolLifeStatus | String | Tool life status |
| rawTimeLimit | Double | Tool life time limit [time] |
| rawTimeLimit1 | Double | Maximum tool life [time]. Heidenhain only. |
| rawTimeLimit2 | Double | Maximum tool life for each tool call [time]. Heidenhain only. |
| rawCurrentTime | Double | Current tool life used [time] |
| rawOverTime | Double | Overtime beyond tool life [time]. Heidenhain only. |
| rawRemainingTime | Double | Remaining tool life [time]. Siemens only. |
| rawPrewarningRemainingTime | Double | Pre-warning remaining tool life [time]. Siemens only. |
| rawRemainingCount | Int32 | Remaining tool life [count]. Siemens only. |
| rawPrewarningRemainingCount | Int32 | Pre-warning remaining tool life [count]. Siemens only. |
| rawRemainingWear | Double | Remaining tool life [machine default length unit]. Siemens only. |
| rawPrewarningRemainingWear | Double | Pre-warning remaining tool life [machine default length unit]. Siemens only. |
| inventoryNum | String | Tool identification code. Heidenhain only. |

> Note: The common data section is equivalent to the response parameters in section 2.5.1.21 `readToolLife`. For supported life types per CNC system, see section 1.2.25 ToolLife.

---

### 2.5.1.24 batchReadToolLifeDetails — Batch Read Tool Life Details

This interface is the batch version of section 2.5.1.23 `readToolLifeDetails`. By specifying a list of target tools in the request body, multiple sets of data can be read in a single call.

```
POST  /api/cnc/batchReadToolLifeDetails?machineID=MACHINEID
```

**Request Body Example** (application/json)

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

**Request Parameters** (same as section 2.5.1.23 `readToolLifeDetails`)

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| groupNum | Int32 | Specify one or more of `groupNum`, `toolIndex`, `toolNum`, or `offsetNum` to identify the target tool |
| toolIndex | Int32 | See above. |
| toolNum | Int32 | See above. |
| offsetNum | Int32 | See above. |

**Response Example**

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

Response parameters are the same as section 2.5.1.23 `readToolLifeDetails`. If a particular request fails, the corresponding position in the response array contains that request's error information.

---

### 2.5.2 Cached Read

When using cached read interfaces, the gateway returns data from the cache corresponding to the configured automatic data collection tasks. To use these interfaces, the gateway local cache feature must be enabled (enabled by default from the factory), and the corresponding auto-collection tasks must be active.

---

### 2.5.2.1 readTaskData — Read Machine Task Data

This interface reads task data for the target machine from the gateway cache, rather than communicating directly with the machine. As a result, in addition to raw data from the machine (such as machine status and alarm information), it can also return gateway-computed data (overload times, OEE data, etc.) with higher operational efficiency. The corresponding task must be enabled first.

```
GET  /api/cnc/readTaskData?machineID=MACHINEID&type=TYPE&tag=TAG
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| type | String | (Required) Data type. See section 1.2 — Data Class Description. |
| tag | String | Tag in field notation. See section 1.2 — Data Class Description. |

**Example**

To retrieve spindle overload data for spindle 1 of the machine with machineID `1010`, use `type=SpindleOverload`, `tag=S1`:

```
/api/cnc/readTaskData?machineID=1010&type=SpindleOverload&tag=S1
```

If the requested task is not enabled, or is enabled but has not executed yet, the response is:

```json
{
  "errorCode": 1303,
  "errorMsg": "Task not ready."
}
```

When operating normally, the data for the specified type is returned in JSON format:

```json
{
  "cumulativeTime": 14367,
  "spindleNo": 1,
  "time": "2024-03-01T02:26:42.2781587Z"
}
```

> Note: In the response, `time` is the timestamp when the data was collected. For other response parameters, see the field and tag descriptions for the corresponding `type` in section 1.2 — Data Class Description.

---

### 2.5.2.2 batchReadTaskData — Batch Read Machine Task Data

This interface is the batch version of section 2.5.2.1 `readTaskData`. By specifying a list of target data in the request body, multiple sets of data can be read in a single call. The corresponding tasks must be enabled first.

```
POST  /api/cnc/batchReadTaskData
```

**Request Body Example** (application/json)

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

**Request Parameters** (same as section 2.5.2.1 `readTaskData`)

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| type | String | (Required) Data type. See section 1.2 — Data Class Description. |
| tag | String | Tag in field notation. See section 1.2 — Data Class Description. |

**Response Example**

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

Results are returned in the same order as the requests. If a particular request fails, the corresponding position in the response array contains that request's error information.

> Note: In the response, `time` is the timestamp when the data was collected. For other response parameters, see section 1.2 — Data Class Description.

---

### 2.5.2.3 readGroupTaskData — Read Machine Group Task Data

This interface reads the latest data for the specified task of the target machine group from the gateway cache. The corresponding machine group task must be enabled first.

```
GET  /api/cnc/readGroupTaskData?groupID=GROUPID&type=TYPE&tag=TAG
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| groupID | String | (Required) Target machine group identifier; see section 1.1.2 groupID — Machine Group Identifier |
| type | String | (Required) Data type. Currently supports: section 1.2.11 `GroupCount`, section 1.2.12 `GroupCumulativeTime`, and section 1.2.13 `GroupOEE`. |
| tag | String | Tag in field notation. See section 1.2 — Data Class Description. |

**Example**

To retrieve machining count data for the machine group with groupID `g2`, use `type=GroupCount`:

```
/api/cnc/readGroupTaskData?groupID=g2&type=GroupCount
```

If the requested task is not enabled or has not yet executed, the response is:

```json
{
  "errorCode": 1303,
  "errorMsg": "Task not ready."
}
```

When operating normally, the data for the specified type is returned:

```json
{
  "cumulativeCount": 127,
  "time": "2024-03-01T02:26:50.1127663Z"
}
```

---

### 2.5.2.4 batchReadGroupTaskData — Batch Read Machine Group Task Data

This interface is the batch version of section 2.5.2.3 `readGroupTaskData`. By specifying a list of target data in the request body, multiple sets of data can be read in a single call. The corresponding tasks must be enabled first.

```
POST  /api/cnc/batchReadTaskData
```

**Request Body Example** (application/json)

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

**Request Parameters** (same as section 2.5.2.1 `readTaskData`)

| Parameter | Type | Description |
|-----------|------|-------------|
| groupID | String | Target machine group identifier; see section 1.1.2 groupID — Machine Group Identifier |
| type | String | Data type. Currently supports `GroupCount`, `GroupCumulativeTime`, and `GroupOEE`. |
| tag | String | Tag in field notation. See section 1.2 — Data Class Description. |

**Response Example**

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

Results are returned in the same order as the requests. If a particular request fails, the corresponding position in the response array contains that request's error information.

---

### 2.5.2.5 batchReadErrors — Batch Read Machine Connection Status

This interface retrieves the current connection status for a list of specified machines in a single call.

```
POST  /api/cnc/batchReadErrors
```

**Request Body Example** (application/json)

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

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |

**Response Example**

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

Results are returned in the same order as the requests.

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| errorCode | Int32 | (Required) Error code. `0` = connection successful. |
| errorMsg | String | (Required) Error message |

---

### 2.5.2.6 readErrorSummary — Read Connection Status Summary

Retrieves a summary of the current machine connection status. No request parameters.

```
GET  /api/cnc/readErrorSummary
```

**Response Example**

```json
{
  "success": 8,
  "offline": 1,
  "error": 0
}
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| success | Int32 | (Required) Number of successfully connected machines |
| offline | Int32 | (Required) Number of offline machines |
| error | Int32 | (Required) Number of machines with connection errors |
