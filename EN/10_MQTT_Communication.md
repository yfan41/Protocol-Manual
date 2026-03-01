## IV. MQTT Communication

Once MQTT communication is enabled and the automatic data collection task for the target machine or machine group is activated (see the *User Manual*, section 5.3.1 Add Machine — task settings for each machine type, and section 5.4.1.2 Machine Group Task Settings), the gateway will convert the collected task result data into JSON-formatted messages based on the MQTT protocol and publish them to the specified MQTT server. The default topic for data reports is `"r"`. The gateway also supports RPC interfaces. Refer to *User Manual* section 5.6.3 MQTT Configuration for instructions on configuring the MQTT service.

---

### 4.1 Data Report Message Format

The gateway MQTT protocol supports multiple message modes, including Default, MKT, TB, TB2, Brm, IoTDA, WisIoT, etc.

The message content structure is as follows:

| Mode | Data Report Message Format |
|------|--------------------------|
| Default | `{ "type": <type>, "unixtimestamp": <time>, "data": { "<field>": <value> }, "<tag>": <value>, "<entityID>": <value> }` |
| MKT | `{ "unixtimestamp": <time>, "<entityID>_<fieldID>_<field>": <value> }` |
| TB | `{ "<alias>": [ { "ts": <time>, "values": { "<fieldID>_<field>": <value> } } ] }` |
| TB2 | `{ "<machineID>": [ { "ts": <time>, "values": { "<fieldID>_<field>": <value> } } ] }` |
| Brm | `[ { "type": <type>, "ts": <time>, "data": { "<field>": <value> }, "<tag>": <value>, "<entityID>": <value> } ]` |
| IoTDA | `{ "devices": [ { "device_id": <entityID>, "services": [ { "service_id": <type>, "properties": { "<tagField>_<field>": <value> }, "event_time": <time> } ] } ] }` |
| WisIoT | `{ "properties": [ { "key": <entityID>_<fieldID>_<field>, "name": <fieldID>_<field>, "value": <value>, "time": <time> } ] }` |

#### Key Concepts

1. **`<entityID>`** — Machine or machine group identifier: If the current data belongs to a machine, `<entityID>` is the machine identifier `machineID`; if it belongs to a machine group, `<entityID>` is the machine group identifier `groupID`. See section 1.1 for machineID and groupID.

2. **`<alias>`** — Machine or machine group name: The user-defined machine name or machine group name (see *User Manual*, sections 5.3.1 Add Machine and 5.4.1 Add Machine Group).

3. **`<type>`** class, **`<field>`** field, and **`<tag>`** tag: A **class** contains at least one **field** and any number of **tags**. A **field** is the name of a data item. When a **class** cannot be distinguished by machine or machine group alone, **tags** are added to distinguish data of that class. For example, the class `SpindleOverload` (Spindle Overload Data) contains the **field** `CumulativeTime` (Cumulative Overload Time) and the **tag** `SpindleNo` (Spindle Number).

4. **`<tagField>`** — Field form of a tag: composed of the **tag** abbreviation and **tag value**. For example, `S1` is the field form of `SpindleNo=1`.

5. **`<fieldID>`** — Field identifier: If the **class** contains no **tags**, the **field identifier** is the same as the **class**. If the **class** contains **tags**, the **field identifier** is composed of the **class** and the **field forms of all tags**, separated by underscores. The order of tags in the field form is determined by the tag's index in the descriptions below. For example, **field identifier** `SpindleOverload_S1` represents overload data for the first spindle, where `S1` is the field form of `SpindleNo=1`; **field identifier** `ToolLife_G1_I2` represents tool life data for the 2nd tool in tool group 1, where `G1_I2` is the field form of `GroupNum=1; Index=2`.

6. **`<time>`** — Timestamp: the time of task execution, in milliseconds.

7. **`<value>`** — Value: a numeric value or content string.

8. In Default and MKT modes, each message contains data from only one `<entityID>` (machine or machine group) and one data class `<type>`. In TB, TB2, IoTDA, Brm, and WisIoT modes, data from different entities `<entityID>` and different data classes `<type>` can be combined into a single message.

---

#### Message Examples

##### Example 1 (Default Mode)

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

- `"type": "CNCStatus"` — Data class. Use "CNCStatus" as the keyword to find section 1.2.4 CNCStatus: Machine Status in section 1.2 Data Description; this message contains machine status data.
- `"unixtimestamp": 1698908463135` — Timestamp of this message.
- `"data": { "cncStatus": "MANUAL", "alarmStatus": "NO_ALARM" }` — Data content. Refer to the data class declared by `<type>` for descriptions of each field.
- `"machineID": "10"` — `<entityID>` machine or machine group identifier; this line indicates the data in this message is from the machine with machineID 10.

##### Example 2 (Default Mode)

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

- `"type": "GroupCount"` — Data class. Use "GroupCount" as the keyword to find section 1.2.11 GroupCount: Machine Group Machining Count. `"cumulativeCount"` is a field under the GroupCount data class, representing the cumulative machining count for the machine group.
- `"groupID": 1` — `<entityID>` here is the group identifier `groupID`, indicating the data in this message is from machine group 1.

##### Example 3 (Default Mode)

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

- `"type": "ToolLife"` — Data class. Use "ToolLife" as the keyword to find section 1.2.27 ToolLife: Tool Life Data.
- `"data"` — `"timeLimit"` is the life limit [seconds], `"currentTime"` is the current life [seconds], and `"prewarningTime"` is the prewarning life [seconds].
- `"toolNum": 9, "toolOffsetNum": 2` — These lines indicate that the tool offset data in this message belongs to tool 9, offset 2.

##### Example 4 (MKT Mode)

```json
{
  "unixtimestamp": 1698908397751,
  "10_ToolLife_T9_02_timeLimit": 1800,
  "10_ToolLife_T9_02_currentTime": 1620,
  "10_ToolLife_T9_02_prewarningTime": 1680
}
```

- `"10_ToolLife_T9_02_timeLimit": 1800` — Format: `<entityID>_<fieldID>_<field>`: `<value>`
  - `10` is the `<entityID>` machine identifier
  - `ToolLife_T9_02` is the `<fieldID>` field identifier (ToolLife is `<type>`, T9_02 is the tag field form, T is the abbreviation for `toolNum`, O is the abbreviation for `toolOffsetNum`)
  - `timeLimit` is the `<field>` (life limit [seconds])

##### Example 5 (TB Mode)

```json
{
  "Machine 10": [
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

- `"Machine 10"` — `<alias>` machine or machine group name. Set by the user in the Add/Edit Machine (Group) window on the gateway machine configuration page. If not set, the default alias is the same as the `entityID`.
- `"ToolLife_T9_02_timeLimit": 1800` in `"values"` — Format: `<fieldID>_<field>`: `<value>`. `ToolLife_T9_02` is `<fieldID>`, `timeLimit` is `<field>`.

##### Example 6 (IoTDA Mode)

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

- `"device_id": "10"` — Format: `"device_id": <entityID>`; here it is the machine identifier `machineID`.
- `"service_id": "ToolLife"` — Format: `"service_id": <type>`; use "ToolLife" to identify this message as tool life data.
- `"T9_02_timeLimit": 1800` — `T9_02` is the `<tagField>` (field form of the tag); T is the abbreviation for `toolNum` and O is the abbreviation for `toolOffsetNum`. `timeLimit` is the `<field>` (life limit [seconds]).
- `"event_time": 1698908397751` — Format: `"event_time": <time>`; the value 1698908397751 is the timestamp of this message.

---

### 4.2 RPC Interface

The gateway supports RPC interfaces based on the MQTT protocol. Users must configure the RPC request topic and RPC response topic in the gateway management page (see *User Manual* section 5.6.3 MQTT Configuration). After configuration, the gateway listens on the RPC request topic and publishes RPC request results to the RPC response topic. RPC interface data encoding uses the encoding set in MQTT configuration.

If you need to embed the RPC request identifier in the topic, use the wildcard `${request_id}` at the desired position.

For example:
- RPC request topic: `request/${request_id}`
- RPC response topic: `response/${request_id}`

With this configuration, when the gateway receives a request on `request/24`, it will publish the result to the topic `response/24`.

> **Note:** It is recommended to place `${request_id}` at the end of the topic. If `${request_id}` must be placed in the middle of the topic, it must be preceded by "/" as a separator.

The gateway MQTT protocol supports multiple message modes, including Default, MKT, Brm, TB, TB2, etc.

#### RPC Request Message Format

| Mode | RPC Request Message Format |
|------|--------------------------|
| Default/MKT/Brm | `{ "id": <request_id>, "method": "<method>", "params": <params> }` |
| TB/TB2 | `{ "device": "<deviceName>", "data": { "id": "<request_id>", "method": "<method>", "params": <params> } }` |
| WisIoT/IoTDA | Not supported |

#### RPC Response Message Format

| Mode | RPC Response Message Format |
|------|-----------------------------|
| Default/MKT/Brm | `{ "id": <request_id>, "data": <response> }` |
| TB/TB2 | `{ "device": "<deviceName>", "id": <request_id>, "data": <response> }` |
| WisIoT/IoTDA | Not supported |

Where `<response>` is the result of the RPC command, `<deviceName>` is the machine name, and `<request_id>` is a custom request identifier. Note that in TB mode, the `"machineID"` parameter in `<params>` is not required; the machine is specified by `<deviceName>`.

---

#### Supported RPC Commands

Except for authentication interfaces and some file management interfaces, RPC commands correspond one-to-one with the interfaces in Chapter II: HTTP Communication. Refer to the HTTP communication interface descriptions for input parameters and return values.

| RPC Command | Description |
|-------------|-------------|
| **Data Read/Write — Direct Read/Write, prefix /cnc/** | |
| readAlarm | Read alarm information |
| readCNCStatus | Read machine status |
| readCNCStatusDetails | Read machine status details |
| readCount | Read machining count |
| readCurrentToolNumber | Read current tool number |
| readEnergyConsumption | Read energy consumption data |
| readFeed | Read feed data |
| readFeedAndSpindle | Read feed and spindle data |
| readLaserPower | Read laser power |
| readLoad | Read load data |
| readLog | Read log information |
| readOffsetData | Read tool offset data |
| batchReadOffsetData | Batch read tool offset data |
| writeOffsetData | Write tool offset data |
| readPosition | Read coordinate data |
| readProgramBlock | Read current program block |
| readProgramInfo | Read current program |
| readPlcData | Read PLC data |
| writePlcData | Write PLC data |
| readTimeData | Read machine time data |
| readToolLife | Read tool life |
| batchReadToolLife | Batch read tool life |
| readToolLifeDetails | Read tool life details |
| batchReadToolLifeDetails | Batch read tool life details |
| **Data Read/Write — Cache Read, prefix /cnc/** | |
| readTaskData | Read machine task data |
| batchReadTaskData | Batch read machine task data |
| readGroupTaskData | Read machine group task data |
| batchReadGroupTaskData | Batch read machine group task data |
| batchReadErrors | Batch read machine connection status |
| readErrorSummary | Read status summary |
| **File Management Interface, prefix /cnc/** | |
| readProgramList | Read machine file list |
| receiveFile | Receive machine file |
| readCurrentProgram | Receive machine's currently running file |
| sendFile | Send file to machine |
| batchSendFile | Batch send files to machine |
| deleteFile | Delete machine file |
| lockFileByRange | Lock/unlock machine program editing |
| createDir | Create machine directory |
| deleteDir | Delete machine directory |
| selectProgram | Select program |
| readAllPrograms | Browse all files |
| searchFile | Search machine files |
| **Machine Data Analysis Interface, prefix /analysis/** | |
| oee | Machine OEE data analysis |
| alarm | Machine alarm data analysis |
| count | Machine count data analysis |
| overall | Machine overall data analysis |
| cycle | Machine cycle time data analysis |
| **Machine Group Data Analysis Interface, prefix /group-analysis/** | |
| oee | Machine group OEE data analysis |
| alarm | Machine group alarm data analysis |
| count | Machine group count data analysis |
| overall | Machine group overall data analysis |
| cycle | Machine group cycle time data analysis |
| **Historical Data Interface, prefix /db/** | |
| machine | Machine historical data |
| group-machine | Historical data for all machines in a group |
| query | Query historical data |
| **Global Configuration Interface, prefix /config/** | |
| gateway-info | Get gateway information |
| settings | Get gateway global settings |
| update-settings | Update gateway global settings |
| security | Get gateway global security settings |
| update-security | Update gateway global security settings |
| **User Configuration Interface, prefix /config/** | |
| user | Get user settings |
| users | Get all user settings |
| create-user | Create new user |
| update-user | Update user settings |
| delete-user | Delete user |
| user-security | Get user security settings |
| update-user-security | Update user security settings |
| **Machine Configuration Interface, prefix /config/** | |
| machine | Get machine configuration |
| machines | Get all machine configurations |
| create-machine | Add machine configuration |
| update-machine | Update machine configuration |
| delete-machine | Delete machine configuration |
| batch-delete-machine | Batch delete machine configurations |
| **Machine Group Configuration Interface, prefix /config/** | |
| group | Get machine group configuration |
| groups | Get all machine group configurations |
| create-group | Add machine group configuration |
| update-group | Update machine group configuration |
| delete-group | Delete machine group configuration |
| batch-delete-group | Batch delete machine group configurations |
| **Task Configuration Interface, prefix /config/** | |
| machine-task-interval-settings | Get machine task interval settings |
| update-machine-task-interval-settings | Update machine task interval settings |
| group-task-interval-settings | Get machine group task interval settings |
| update-group-task-interval-settings | Update machine group task interval settings |
| oee-monitoring-settings | Get OEE monitoring settings |
| update-oee-monitoring-settings | Update OEE monitoring settings |
| tool-life-monitoring-settings | Get tool life monitoring settings |
| update-oee-monitoring-settings | Update tool life monitoring settings |
| count-monitoring-settings | Get machining count monitoring settings |
| update-count-monitoring-settings | Update machining count monitoring settings |
| overload-monitoring-settings | Get overload monitoring settings |
| update-overload-monitoring-settings | Update overload monitoring settings |
| alarm-monitoring-settings | Get alarm monitoring settings |
| update-alarm-monitoring-settings | Update alarm monitoring settings |
| machine-status-monitoring-settings | Get machine status monitoring settings |
| update-machine-status-monitoring-settings | Update machine status monitoring settings |
| **Communication Configuration Interface, prefix /config/** | |
| cloud-settings | Get cloud platform settings |
| update-cloud-settings | Update cloud platform settings |
| modbus-settings | Get MODBUS settings |
| update-modbus-settings | Update MODBUS settings |
| mqtt-settings | Get MQTT settings |
| update-mqtt-settings | Update MQTT settings |
| database-settings | Get database settings |
| update-database-settings | Update database settings |
| gateway-file-server-settings | Get gateway file server settings |
| update-gateway-file-server-settings | Update gateway file server settings |
| http-settings | Get HTTP settings |
| update-http-settings | Update HTTP settings |
| hub-settings | Get Hub settings |
| update-hub-settings | Update Hub settings |
| remote-access | Get remote access settings |
| update-remote-access | Update remote access settings |
| **Core Service Function Interface, prefix /core/** | |
| info | Get Core service information |
| license-info | Get license information |
| service-status | Get service status |
| upload-license | Upload gateway license |
| **Gateway Service Function Interface, prefix /gateway/** | |
| alias | Get gateway name |
| update-alias | Update gateway name |
| reboot | Reboot gateway hardware |
| restart | Restart all services |
| restart-service | Restart Core service |
| shut-down | Shut down gateway |
| internet-connection | Get gateway network status |
| time | Get gateway current time |
| sync-time | Synchronize gateway time |
| time-zone | Get gateway time zone |
| time-zones | Get time zone options |
| update-time-zone | Update gateway time zone |
| network-adapters | Get gateway network adapter list |
| lan | Get wired network settings |
| update-lan | Update gateway wired network settings |
| wifi | Get wireless network settings |
| update-wifi | Update wireless network settings |
| search-wifi | Search for wireless networks |
| connect-wifi | Connect to wireless network |
| disconnect-wifi | Disconnect from wireless network |
| static-routing | Get static routing settings |
| update-static-routing | Update static routing settings |
| connect-remote-host | Connect to remote server |
| disconnect-remote-host | Disconnect from remote server |
| file-server-items | Get gateway file server list |
| delete-file-server-item | Delete gateway file server item |

---

#### RPC Examples

##### Example 1 (Default Mode — sendFile: Send File to Machine)

In Default mode, to send a file with ASCII-encoded content "fileContent" to the machine with machineID 1010, saving it as "2222" in the directory `Dir/Prog`, use the request topic `"request/1"` where 1 is a custom request identifier.

Request message:

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

Response topic `"response/1"`, response message:

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

`machineID`, `fileName`, and `dirAtCNC` are request parameters from the URL of HTTP interface section 2.6.6 sendFile, embedded directly in the `"params"` field. The POST request body should be embedded in the `"data"` field inside `"params"`.

##### Example 2 (Default Mode — writeOffsetData: Write Tool Offset Data)

In Default mode, to write tool offset data for offset number 1, use the request topic `"request/2"` where 2 is a custom request identifier.

Request message:

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

Response topic `"response/2"`, response message:

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

`machineID` and `offsetNum` are parameters from the URL of HTTP interface section 2.5.1.14 writeOffsetData, embedded directly in the `"params"` field. The POST request body should be embedded in the `"data"` field inside `"params"`.
