## 2.7 Data Analysis Interface

The data analysis interface is used to retrieve data analysis results for a target machine or machine group within a specified time range.

Before using the data analysis interface, the gateway local cache must be enabled (see the *User Manual*, section 5.2.1 Settings — Advanced Options) to allow machine status history data to be saved locally on the gateway.

Using the data analysis interface requires specifying a start timestamp and an end timestamp. The maximum range between them is 31 days. The maximum local retention period is 365 days; therefore, the start time cannot be earlier than 365 days before the current time.

If the current timestamp is less than the specified end timestamp, the current timestamp is automatically used as the end timestamp.

If the gateway did not receive machine or machine group data for a period (data gap), the data returned by this interface may not match actual conditions. Common causes of data gaps include: the gateway local cache is not enabled, the corresponding automatic data collection task is not enabled, the gateway lost power, the gateway network was interrupted, and the machine being offline.

---

## 2.7.1 Machine Data Analysis

Machine data analysis is used to retrieve data analysis for a target machine within a specified time range. Base URL: `/api/analysis`.

### 2.7.1.1 oee — Machine OEE Data Analysis

Retrieves machine OEE statistics for each grouping interval within the specified time range.

```
GET  /api/analysis/oee?machineID=MACHINEID&startUnix=STARTUNIX&endUnix=ENDUNIX&interval=INTERVAL
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| startUnix | Int64 | (Required) Start timestamp [seconds] |
| endUnix | Int64 | (Required) End timestamp [seconds] |
| interval | Int32 | Grouping interval [seconds]. If not specified, there is only one grouping interval. |

**Response Example**

```json
[
  {
    "machineName": "Simulated Machine 1",
    "startTime": "2022-04-28T14:30:00Z",
    "endTime": "2022-04-28T15:30:00Z",
    "availability": 83.33333,
    "offTime": 100,
    "waitTime": 200,
    "emergencyTime": 300,
    "autoRunTime": 3000,
    "manualTime": 0.0
  },
  {
    "machineName": "Simulated Machine 1",
    "startTime": "2022-04-28T15:30:00Z",
    "endTime": "2022-04-28T16:30:00Z",
    "availability": 0.0,
    "offTime": 0,
    "waitTime": 3600,
    "emergencyTime": 0,
    "autoRunTime": 0,
    "manualTime": 0
  }
]
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineName | String | (Required) Machine name, set in the Add/Edit Device window on the Machine Configuration page. |
| startTime | String | (Required) Grouping interval start time (UTC) |
| endTime | String | (Required) Grouping interval end time (UTC) |
| availability | Float | (Required) Availability [%] |
| offTime | Int64 | (Required) Off or offline time [seconds] |
| waitTime | Int64 | (Required) Wait time [seconds] |
| emergencyTime | Int64 | (Required) Emergency stop time [seconds] |
| autoRunTime | Int64 | (Required) Auto run time [seconds] |
| manualTime | Int64 | (Required) Setup time [seconds] |

> Note: For Machine OEE data descriptions, see the *User Manual*, section 6.1.1 Machine OEE Data. If status data is missing for a period between the start and end times, that period is treated as off or offline.

---

### 2.7.1.2 alarm — Machine Alarm Data Analysis

Retrieves the alarm history for the machine within the specified time range.

```
GET  /api/analysis/alarm?machineID=MACHINEID&startUnix=STARTUNIX&endUnix=ENDUNIX
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| startUnix | Int64 | (Required) Start timestamp [seconds] |
| endUnix | Int64 | (Required) End timestamp [seconds] |

**Response Example**

```json
[
  {
    "machineName": "Simulated Machine 1",
    "startTime": "2023-06-16T16:00:00Z",
    "endTime": "2023-06-16T16:00:10Z",
    "alarmMsg": "00:00 ALARM 1",
    "alarmLevel": "WRN"
  },
  {
    "machineName": "Simulated Machine 1",
    "startTime": "2023-06-16T16:00:00Z",
    "endTime": "2023-06-16T16:00:10.001Z",
    "alarmMsg": "00:00 ALARM 2",
    "alarmLevel": "WRN"
  },
  {
    "machineName": "Simulated Machine 1",
    "startTime": "2023-06-16T16:01:00.008Z",
    "endTime": "2023-06-16T16:01:50.019Z",
    "alarmMsg": "00:01 ALARM 1",
    "alarmLevel": "WRN"
  }
]
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineName | String | (Required) Machine name, set in the Add/Edit Device window on the Machine Configuration page. |
| startTime | String | (Required) Alarm start time (UTC) |
| endTime | String | (Required) Alarm end time (UTC) |
| alarmMsg | String | (Required) Alarm content |
| alarmLevel | String | (Required) Alarm level. Three levels in descending priority: Error (ERR), Warning (WRN), Information (INF). Refer to *User Manual* section 5.5.7 Alarm Monitoring Settings to assign levels using keywords and filter alarms below the minimum level. Alarms without an assigned level default to Warning (WRN). |

> Note: The period from alarm activation to alarm clearance constitutes one alarm record, including its start time, end time, alarm content, and alarm level. If an alarm was already active before the start timestamp and has not been cleared, the start time of that alarm record is the time corresponding to the start timestamp. If an alarm has not been cleared at the end timestamp, the end time is the time corresponding to the end timestamp. If alarm data is missing between the start and end times, no alarms are assumed to have occurred during that missing period.

---

### 2.7.1.3 count — Machine Count Data Analysis

Retrieves the cumulative machining count for each grouping interval within the specified time range.

```
GET  /api/analysis/count?machineID=MACHINEID&startUnix=STARTUNIX&endUnix=ENDUNIX&interval=INTERVAL
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| startUnix | Int64 | (Required) Start timestamp [seconds] |
| endUnix | Int64 | (Required) End timestamp [seconds] |
| interval | Int32 | Grouping interval [seconds]. If not specified, there is only one grouping interval. |

**Response Example**

```json
[
  {
    "machineName": "Simulated Machine 1",
    "startTime": "2022-04-28T14:30:00Z",
    "endTime": "2022-04-28T15:30:00Z",
    "count": 96
  },
  {
    "machineName": "Simulated Machine 1",
    "startTime": "2022-04-28T15:30:00Z",
    "endTime": "2022-04-28T16:30:00Z",
    "count": 0
  }
]
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineName | String | (Required) Machine name, set in the Add/Edit Device window on the Machine Configuration page. |
| startTime | String | (Required) Grouping interval start time (UTC) |
| endTime | String | (Required) Grouping interval end time (UTC) |
| count | Int32 | (Required) Cumulative machining count; the difference between `cumulativeCount` at the beginning and end of the grouping interval |

---

### 2.7.1.4 overall — Machine Overall Data Analysis

Retrieves comprehensive data for each grouping interval within the specified time range, combining OEE analysis (section 2.7.1.1) and count analysis (section 2.7.1.3).

```
GET  /api/analysis/overall?machineID=MACHINEID&startUnix=STARTUNIX&endUnix=ENDUNIX&interval=INTERVAL
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| startUnix | Int64 | (Required) Start timestamp [seconds] |
| endUnix | Int64 | (Required) End timestamp [seconds] |
| interval | Int32 | Grouping interval [seconds]. If not specified, there is only one grouping interval. |

**Response Example**

```json
[
  {
    "machineName": "Simulated Machine 1",
    "startTime": "2022-04-28T14:30:00Z",
    "endTime": "2022-04-28T15:30:00Z",
    "count": 96,
    "availability": 83.33333,
    "offTime": 100,
    "waitTime": 200,
    "emergencyTime": 300,
    "autoRunTime": 3000,
    "manualTime": 0.0
  },
  {
    "machineName": "Simulated Machine 1",
    "startTime": "2022-04-28T15:30:00Z",
    "endTime": "2022-04-28T16:30:00Z",
    "count": 0,
    "availability": 0.0,
    "offTime": 0,
    "waitTime": 3600,
    "emergencyTime": 0,
    "autoRunTime": 0,
    "manualTime": 0
  }
]
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineName | String | (Required) Machine name |
| startTime | String | (Required) Grouping interval start time (UTC) |
| endTime | String | (Required) Grouping interval end time (UTC) |
| count | Int32 | (Required) Cumulative machining count |
| availability | Float | (Required) Availability [%] |
| offTime | Int64 | (Required) Off or offline time [seconds] |
| waitTime | Int64 | (Required) Wait time [seconds] |
| emergencyTime | Int64 | (Required) Emergency stop time [seconds] |
| autoRunTime | Int64 | (Required) Auto run time [seconds] |
| manualTime | Int64 | (Required) Setup time [seconds] |

---

### 2.7.1.5 cycle — Machine Cycle Time Data Analysis

Retrieves cycle time data for the machine within the specified time range.

```
GET  /api/analysis/cycle?machineID=MACHINEID&startUnix=STARTUNIX&endUnix=ENDUNIX
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| startUnix | Int64 | (Required) Start timestamp [seconds] |
| endUnix | Int64 | (Required) End timestamp [seconds] |

**Response Example**

```json
[
  {
    "machineName": "Simulated Machine 1",
    "startTime": "2025-05-23T06:35:44.046Z",
    "endTime": "2025-05-23T06:36:25.046Z",
    "lastCycleTime": 41,
    "mainPrgmName": "06495",
    "machineID": "1"
  },
  {
    "machineName": "Simulated Machine 1",
    "startTime": "2025-05-23T06:36:25.072Z",
    "endTime": "2025-05-23T06:36:56.072Z",
    "lastCycleTime": 31,
    "mainPrgmName": "06495",
    "machineID": "1"
  }
]
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineName | String | (Required) Machine name, set in the Add/Edit Device window on the Machine Configuration page. |
| startTime | String | (Required) Cycle start time (UTC) |
| endTime | String | (Required) Cycle end time (UTC) |
| lastCycleTime | Int32 | (Required) Cycle duration [seconds]; only auto run time is counted. |
| mainPrgmName | String | (Required) Main program name executed during this cycle |
| machineID | String | Machine machineID |

---

## 2.7.2 Machine Group Data Analysis

Machine group data analysis retrieves data analysis for target machines or machine groups within a specified time range. Base URL: `/api/group-analysis`. Replace the machine identifier `machineID` with the machine group identifier `groupID`.

### 2.7.2.1 oee — Machine Group OEE Data Analysis

Retrieves the machine OEE data for all machines in the machine group, for each grouping interval within the specified time range.

```
GET  /api/group-analysis/oee?groupID=GROUPID&startUnix=STARTUNIX&endUnix=ENDUNIX&interval=INTERVAL
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| groupID | String | (Required) Target machine group identifier; see section 1.1.2 groupID — Machine Group Identifier |
| startUnix | Int64 | (Required) Start timestamp [seconds] |
| endUnix | Int64 | (Required) End timestamp [seconds] |
| interval | Int32 | Grouping interval [seconds]. If not specified, there is only one grouping interval. |

**Response Example**

```json
[
  {
    "machineID": "1010",
    "machineName": "Simulated Machine 1",
    "startTime": "2022-04-28T14:30:00Z",
    "endTime": "2022-04-28T15:30:00Z",
    "availability": 83.33333,
    "offTime": 100,
    "waitTime": 200,
    "emergencyTime": 300,
    "autoRunTime": 3000,
    "manualTime": 0.0
  },
  {
    "machineID": "1010",
    "machineName": "Simulated Machine 1",
    "startTime": "2022-04-28T15:30:00Z",
    "endTime": "2022-04-28T16:30:00Z",
    "availability": 0.0,
    "offTime": 0,
    "waitTime": 3600,
    "emergencyTime": 0,
    "autoRunTime": 0,
    "manualTime": 0
  },
  {
    "machineID": "1011",
    "machineName": "Simulated Machine 2",
    "startTime": "2022-04-28T14:30:00Z",
    "endTime": "2022-04-28T15:30:00Z",
    "availability": 80.55556,
    "offTime": 150,
    "waitTime": 250,
    "emergencyTime": 300,
    "autoRunTime": 2900,
    "manualTime": 0.0
  }
]
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Machine machineID; see section 1.1.1 machineID — Machine Identifier |
| machineName | String | (Required) Machine name, set in the Add/Edit Device window on the Machine Configuration page. |
| startTime | String | (Required) Grouping interval start time |
| endTime | String | (Required) Grouping interval end time |
| availability | Float | (Required) Availability [%] |
| offTime | Int64 | (Required) Off or offline time [seconds] |
| waitTime | Int64 | (Required) Wait time [seconds] |
| emergencyTime | Int64 | (Required) Emergency stop time [seconds] |
| autoRunTime | Int64 | (Required) Auto run time [seconds] |
| manualTime | Int64 | (Required) Setup time [seconds] |

---

### 2.7.2.2 alarm — Machine Group Alarm Data Analysis

Retrieves the alarm history for all machines in the machine group within the specified time range.

```
GET  /api/group-analysis/alarm?groupID=GROUPID&startUnix=STARTUNIX&endUnix=ENDUNIX
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| groupID | String | (Required) Target machine group identifier; see section 1.1.2 groupID — Machine Group Identifier |
| startUnix | Int64 | (Required) Start timestamp [seconds] |
| endUnix | Int64 | (Required) End timestamp [seconds] |

**Response Example**

```json
[
  {
    "machineID": "1010",
    "machineName": "Simulated Machine 1",
    "startTime": "2023-06-16T16:00:00Z",
    "endTime": "2023-06-16T16:00:10Z",
    "alarmMsg": "00:00 ALARM 1",
    "alarmLevel": "WRN"
  },
  {
    "machineID": "1010",
    "machineName": "Simulated Machine 1",
    "startTime": "2023-06-17T15:59:48.105Z",
    "endTime": "2023-06-17T16:00:00Z",
    "alarmMsg": "23:59 ALARM 2",
    "alarmLevel": "WRN"
  },
  {
    "machineID": "1011",
    "machineName": "Simulated Machine 2",
    "startTime": "2023-06-16T16:00:00.399Z",
    "endTime": "2023-06-16T16:00:30.395Z",
    "alarmMsg": "00:00 ALARM 1",
    "alarmLevel": "WRN"
  }
]
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| machineName | String | Machine name, set in the Add/Edit Device window on the Machine Configuration page. |
| startTime | String | (Required) Alarm start time (UTC) |
| endTime | String | (Required) Alarm end time (UTC) |
| alarmMsg | String | (Required) Alarm content |
| alarmLevel | String | (Required) Alarm level. If no alarms, returns an empty Array. Three levels in descending priority: Error (ERR), Warning (WRN), Information (INF). Alarms without an assigned level default to Warning (WRN). |

---

### 2.7.2.3 count — Machine Group Count Data Analysis

Retrieves the machining count data for all machines in the machine group, for each grouping interval within the specified time range.

```
GET  /api/group-analysis/count?groupID=GROUPID&startUnix=STARTUNIX&endUnix=ENDUNIX&interval=INTERVAL
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| groupID | String | (Required) Target machine group identifier; see section 1.1.2 groupID — Machine Group Identifier |
| startUnix | Int64 | (Required) Start timestamp [seconds] |
| endUnix | Int64 | (Required) End timestamp [seconds] |
| interval | Int32 | Grouping interval [seconds]. If not specified, there is only one grouping interval. |

**Response Example**

```json
[
  {
    "machineID": "1010",
    "machineName": "Simulated Machine 1",
    "startTime": "2022-04-28T14:30:00Z",
    "endTime": "2022-04-28T15:30:00Z",
    "count": 96
  },
  {
    "machineID": "1010",
    "machineName": "Simulated Machine 1",
    "startTime": "2022-04-28T15:30:00Z",
    "endTime": "2022-04-28T16:30:00Z",
    "count": 0
  },
  {
    "machineID": "1011",
    "machineName": "Simulated Machine 2",
    "startTime": "2022-04-28T14:30:00Z",
    "endTime": "2022-04-28T15:30:00Z",
    "count": 52
  }
]
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Machine machineID; see section 1.1.1 machineID — Machine Identifier |
| machineName | String | (Required) Machine name, set in the Add/Edit Device window on the Machine Configuration page. |
| startTime | String | (Required) Grouping interval start time |
| endTime | String | (Required) Grouping interval end time |
| count | Int32 | (Required) Cumulative machining count; the difference between `cumulativeCount` at the beginning and end of the specified time range |

---

### 2.7.2.4 overall — Machine Group Overall Data Analysis

Retrieves comprehensive data for all machines in the machine group, for each grouping interval within the specified time range.

```
GET  /api/group-analysis/overall?groupID=GROUPID&startUnix=STARTUNIX&endUnix=ENDUNIX&interval=INTERVAL
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| groupID | String | (Required) Target machine group identifier; see section 1.1.2 groupID — Machine Group Identifier |
| startUnix | Int64 | (Required) Start timestamp [seconds] |
| endUnix | Int64 | (Required) End timestamp [seconds] |
| interval | Int32 | Grouping interval [seconds]. If not specified, there is only one grouping interval. |

**Response Example**

```json
[
  {
    "machineID": "1010",
    "machineName": "Simulated Machine 1",
    "startTime": "2022-04-28T14:30:00Z",
    "endTime": "2022-04-28T15:30:00Z",
    "count": 96,
    "availability": 83.33333,
    "offTime": 100,
    "waitTime": 200,
    "emergencyTime": 300,
    "autoRunTime": 3000,
    "manualTime": 0.0
  },
  {
    "machineID": "1011",
    "machineName": "Simulated Machine 2",
    "startTime": "2022-04-28T14:30:00Z",
    "endTime": "2022-04-28T15:30:00Z",
    "count": 52,
    "availability": 80.55556,
    "offTime": 150,
    "waitTime": 250,
    "emergencyTime": 300,
    "autoRunTime": 2900,
    "manualTime": 0.0
  }
]
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Machine machineID; see section 1.1.1 machineID — Machine Identifier |
| machineName | String | (Required) Machine name, set in the Add/Edit Device window on the Machine Configuration page. |
| startTime | String | (Required) Grouping interval start time |
| endTime | String | (Required) Grouping interval end time |
| count | Int32 | (Required) Cumulative machining count; the difference between `cumulativeCount` at the beginning and end of the specified time range |
| availability | Float | (Required) Availability [%] |
| offTime | Int64 | (Required) Off or offline time [seconds] |
| waitTime | Int64 | (Required) Wait time [seconds] |
| emergencyTime | Int64 | (Required) Emergency stop time [seconds] |
| autoRunTime | Int64 | (Required) Auto run time [seconds] |
| manualTime | Int64 | (Required) Setup time [seconds] |

---

### 2.7.2.5 cycle — Machine Group Cycle Time Data Analysis

Retrieves cycle time data for all machines in the machine group within the specified time range.

```
GET  /api/group-analysis/cycle?groupID=GROUPID&startUnix=STARTUNIX&endUnix=ENDUNIX
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| groupID | String | (Required) Target machine group identifier; see section 1.1.2 groupID — Machine Group Identifier |
| startUnix | Int64 | (Required) Start timestamp [seconds] |
| endUnix | Int64 | (Required) End timestamp [seconds] |

**Response Example**

```json
[
  {
    "machineName": "Simulated Machine 1",
    "startTime": "2025-05-23T06:35:44.046Z",
    "endTime": "2025-05-23T06:36:25.046Z",
    "lastCycleTime": 41,
    "mainPrgmName": "06495",
    "machineID": "1"
  },
  {
    "machineName": "Simulated Machine 1",
    "startTime": "2025-05-23T06:36:25.072Z",
    "endTime": "2025-05-23T06:36:56.072Z",
    "lastCycleTime": 31,
    "mainPrgmName": "06495",
    "machineID": "1"
  },
  {
    "machineName": "Simulated Machine 2",
    "startTime": "2025-05-23T07:51:50.777Z",
    "endTime": "2025-05-23T07:52:10.777Z",
    "lastCycleTime": 20,
    "mainPrgmName": "02228",
    "machineID": "2"
  }
]
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Machine machineID; see section 1.1.1 machineID — Machine Identifier |
| machineName | String | (Required) Machine name, set in the Add/Edit Device window on the Machine Configuration page. |
| startTime | String | (Required) Cycle start time (UTC) |
| endTime | String | (Required) Cycle end time (UTC) |
| lastCycleTime | Int32 | (Required) Cycle duration [seconds]; only auto run time is counted. |
| mainPrgmName | String | (Required) Main program name executed during this cycle |
