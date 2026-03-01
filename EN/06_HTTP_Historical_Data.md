## 2.8 Historical Data Interface

The historical data interface is used to retrieve historical data for a target machine or machine group within a specified time range. The base URL is `/api/db`.

Before using the historical data interface, the gateway local cache must be enabled (see the *User Manual*, section 5.2.1 Settings — Advanced Options) to allow historical data to be saved locally on the gateway.

Using the historical data interface requires specifying a start timestamp and an end timestamp. The maximum range between them is 31 days. The maximum local retention period is 365 days; therefore, the start time cannot be earlier than 365 days before the current time.

If the current timestamp is less than the specified end timestamp, the current timestamp is automatically used as the end timestamp.

If the gateway did not receive machine or machine group data for a period (data gap), the historical data returned by this interface may not match actual conditions. Common causes of data gaps include: the gateway local cache is not enabled, the corresponding automatic data collection task is not enabled, the gateway lost power, the gateway network was interrupted, and the machine being offline.

---

## 2.8.1 machine — Machine Historical Data

Retrieves historical data for a target machine within a specified time range.

```
GET  /api/db/machine?machineID=MACHINEID&type=TYPE&startUnix=STARTUNIX&endUnix=ENDUNIX
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| type | String | (Required) Data type; see section 1.2 Data Description |
| startUnix | Int64 | (Required) Start timestamp [seconds] |
| endUnix | Int64 | (Required) End timestamp [seconds] |

**Example**

To retrieve the machine status history of the machine with machineID 1010 from 10:00 to 11:00 AM on March 1, 2024, with type=CNCStatus, use the following request:

```
/api/db/machine?machineID=1010&type=CNCStatus&startUnix=1709258400&endUnix=1709262000
```

If no historical data exists for the target machine in the specified time range, an empty array is returned:

```json
[]
```

If historical data exists, the data of the specified type is returned in JSON format:

```json
[
  {
    "cncStatus": "MANUAL_INC",
    "adjustedStatus": "MANUAL_INC",
    "alarmStatus": "ALARM",
    "alarmLevel": "WRN",
    "offTime": 2329,
    "waitTime": 3212,
    "emergencyTime": 232,
    "autoRunTime": 1336,
    "manualTime": 1232,
    "machineID": "2112",
    "time": "2024-03-01T02:22:11.442Z"
  },
  {
    "cncStatus": "MANUAL_INC",
    "adjustedStatus": "MANUAL_INC",
    "alarmStatus": "ALARM",
    "alarmLevel": "WRN",
    "offTime": 2329,
    "waitTime": 3212,
    "emergencyTime": 232,
    "autoRunTime": 1336,
    "manualTime": 1237,
    "machineID": "2112",
    "time": "2024-03-01T02:22:16.44Z"
  },
  ...
]
```

In the response, `machineID` is the machine's machineID (see section 1.1 Basic Description) and `time` is the data acquisition time. Other response parameters correspond to the `field` and `tag` contents of the matching `type` in section 1.2 Data Description.

---

## 2.8.2 group-machine — Historical Data for All Machines in a Group

Retrieves historical data for all machines in a target machine group within a specified time range.

```
GET  /api/db/group-machine?groupID=GROUPID&type=TYPE&startUnix=STARTUNIX&endUnix=ENDUNIX
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| groupID | String | (Required) Target machine group identifier; see section 1.1.2 groupID — Machine Group Identifier |
| type | String | (Required) Data type; see section 1.2 Data Description |
| startUnix | Int64 | (Required) Start timestamp [seconds] |
| endUnix | Int64 | (Required) End timestamp [seconds] |

**Example**

To retrieve the machine status history of all machines in the machine group with groupID g1 from 10:00 to 11:00 AM on March 1, 2024, with type=CNCStatus, use the following request:

```
/api/db/group-machine?groupID=g1&type=CNCStatus&startUnix=1709258400&endUnix=1709262000
```

If no historical data exists in the specified time range, an empty array is returned:

```json
[]
```

If historical data exists, the data of the specified type is returned in JSON format:

```json
[
  {
    "cncStatus": "MANUAL_INC",
    "adjustedStatus": "MANUAL_INC",
    "alarmStatus": "ALARM",
    "alarmLevel": "WRN",
    "offTime": 2329,
    "waitTime": 3212,
    "emergencyTime": 232,
    "autoRunTime": 1336,
    "manualTime": 1232,
    "machineID": "2112",
    "time": "2024-03-01T02:22:11.442Z"
  },
  {
    "cncStatus": "MANUAL_INC",
    "adjustedStatus": "MANUAL_INC",
    "alarmStatus": "ALARM",
    "alarmLevel": "WRN",
    "offTime": 2329,
    "waitTime": 3212,
    "emergencyTime": 232,
    "autoRunTime": 1336,
    "manualTime": 1237,
    "machineID": "2112",
    "time": "2024-03-01T02:22:16.44Z"
  },
  ...
  {
    "cncStatus": "AUTO_RUN",
    "adjustedStatus": "AUTO_RUN",
    "alarmStatus": "NO_ALARM",
    "offTime": 12229,
    "waitTime": 12312,
    "emergencyTime": 2332,
    "autoRunTime": 52332,
    "manualTime": 6132,
    "machineID": "3",
    "time": "2024-03-01T02:19:29.308Z"
  },
  ...
]
```

In the response, `machineID` is the machine's machineID (see section 1.1 Basic Description) and `time` is the data acquisition time. Other response parameters correspond to the `field` and `tag` contents of the matching `type` in section 1.2 Data Description.

---

## 2.8.3 query — Query Historical Data

Queries historical data that meets specified conditions, similar to a database query command.

```
POST  /api/db/query?groupID=GROUPID&type=TYPE&startUnix=STARTUNIX&endUnix=ENDUNIX
```

**Request Body Example (application/json)**

Query data of type "PLC" with timestamps between 1704892831–1708893358, machineID equal to simulated machine 1, and tag equal to Ttag1 or Ttag2, returning the 2 oldest records (ascending by time, limited to 2):

```json
{
  "type": "PLC",
  "startUnix": 1704892831,
  "endUnix": 1708893358,
  "filters": [
    {
      "key": "machineID",
      "operator": "equal",
      "value": "Simulated Machine 1"
    },
    {
      "key": "tag",
      "operator": "in",
      "value": [
        "Ttag1",
        "Ttag2"
      ]
    }
  ],
  "orderBy": {
    "field": "time",
    "isDesc": false
  },
  "limit": 2
}
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| type | String | (Required) Data type; see section 1.2 Data Description |
| startUnix | Int64 | (Required) Start timestamp [seconds] |
| endUnix | Int64 | (Required) End timestamp [seconds] |
| filters | Object[] | (Required) Filter conditions |
| key | String | (Required) Key |
| operator | String | (Required) Comparison operator |
| value | Object | (Required) Comparison value; type is String or String[] |
| orderBy | Object | (Required) Sort method |
| field | String | (Required) Sort field |
| isDesc | Bool | (Required) true: descending; false: ascending |
| limit | Int32 | (Required) Maximum number of results |

**Response Example**

```json
[
  {
    "data": [
      255
    ],
    "tag": "Ttag1",
    "machineID": "Simulated Machine 1",
    "time": "2025-04-28T06:30:19.827Z"
  },
  {
    "data": [
      2147483647
    ],
    "tag": "Ttag2",
    "machineID": "Simulated Machine 1",
    "time": "2025-04-28T06:30:19.848Z"
  }
]
```

In the response, `machineID` is the machine's machineID (see section 1.1 Basic Description) and `time` is the data acquisition time. Other response parameters correspond to the `field` and `tag` contents of the matching `type` in section 1.2 Data Description.
