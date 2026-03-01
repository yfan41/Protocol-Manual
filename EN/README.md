# Bivrost IoT Gateway Communication Protocol v1.19.4
<div align="center">
  <h3>Bivrost Technologies Co., Ltd.</h3>
  Phone: 18824672282<br>
  <img width="200" height="200" alt="image" src="https://github.com/user-attachments/assets/5669fd4a-db92-4f91-b94b-3b422271b9a7" />
</div>

---

> The information contained in this document is proprietary to Bivrost Technologies and is disclosed in confidence. No person may use or disclose to any other person or reproduce this information without obtaining the express written consent of Bivrost Technologies.
>
> © BIVROST TECHNOLOGIES Company Limited, 2026

---

## Table of Contents

- [I. Important Notes](01_Important_Notes.md)
  - [1.1 Identifier Description](01_Important_Notes.md#11-identifier-description)
    - [1.1.1 machineID — Machine Identifier](01_Important_Notes.md#111-machineid--machine-identifier)
    - [1.1.2 groupID — Machine Group Identifier](01_Important_Notes.md#112-groupid--machine-group-identifier)
    - [1.1.3 slaveID — Slave Station Identifier](01_Important_Notes.md#113-slaveid--slave-station-identifier)
    - [1.1.4 ID — Database Identifier](01_Important_Notes.md#114-id--database-identifier)
  - [1.2 Data Description](01_Important_Notes.md#12-data-description)
    - [1.2.1 AlarmHistory: Alarm History](01_Important_Notes.md#121-alarmhistory-alarm-history)
    - [1.2.2 AlarmLog: Current Alarms](01_Important_Notes.md#122-alarmlog-current-alarms)
    - [1.2.3 AxialOverload: Servo Axis Overload Data](01_Important_Notes.md#123-axialoverload-servo-axis-overload-data)
    - [1.2.4 CNCStatus: Machine Status](01_Important_Notes.md#124-cncstatus-machine-status)
    - [1.2.5 Count: Machining Count](01_Important_Notes.md#125-count-machining-count)
    - [1.2.6 CurrentToolNumber: Current Tool Number](01_Important_Notes.md#126-currenttoolnumber-current-tool-number)
    - [1.2.7 Cycle: Cycle Time Data](01_Important_Notes.md#127-cycle-cycle-time-data)
    - [1.2.8 EnergyConsum: Energy Consumption Data](01_Important_Notes.md#128-energyconsum-energy-consumption-data)
    - [1.2.9 Feed: Feed Data](01_Important_Notes.md#129-feed-feed-data)
    - [1.2.10 FeedAndSpindle: Feed Override Data](01_Important_Notes.md#1210-feedandspindle-feed-override-data)
    - [1.2.11 GroupCount: Machine Group Machining Count](01_Important_Notes.md#1211-groupcount-machine-group-machining-count)
    - [1.2.12 GroupCumulativeTime: Machine Group Cumulative Status Time](01_Important_Notes.md#1212-groupcumulativetime-machine-group-cumulative-status-time)
    - [1.2.13 GroupOEE: Machine Group OEE](01_Important_Notes.md#1213-groupoee-machine-group-oee)
    - [1.2.14 LaserPower: Laser Power](01_Important_Notes.md#1214-laserpower-laser-power)
    - [1.2.15 Load: Load Data](01_Important_Notes.md#1215-load-load-data)
    - [1.2.16 LogHistory: Log History](01_Important_Notes.md#1216-loghistory-log-history)
    - [1.2.17 OEE: Machine OEE Data](01_Important_Notes.md#1217-oee-machine-oee-data)
    - [1.2.18 Offset: Tool Offset Data](01_Important_Notes.md#1218-offset-tool-offset-data)
    - [1.2.19 PLC: PLC Data](01_Important_Notes.md#1219-plc-plc-data)
    - [1.2.20 Position: Coordinate Data](01_Important_Notes.md#1220-position-coordinate-data)
    - [1.2.21 ProgramBlock: Program Block](01_Important_Notes.md#1221-programblock-program-block)
    - [1.2.22 ProgramInfo: Current Program](01_Important_Notes.md#1222-programinfo-current-program)
    - [1.2.23 SpindleOverLoad: Spindle Overload Data](01_Important_Notes.md#1223-spindleoverload-spindle-overload-data)
    - [1.2.24 TimeData: Machine Time Data](01_Important_Notes.md#1224-timedata-machine-time-data)
    - [1.2.25 ToolLife: Tool Life Data](01_Important_Notes.md#1225-toollife-tool-life-data)
  - [1.3 Variable Description](01_Important_Notes.md#13-variable-description)
    - [1.3.1 Machine Status](01_Important_Notes.md#131-machine-status)
    - [1.3.2 Alarm Information](01_Important_Notes.md#132-alarm-information)
    - [1.3.3 Service Information](01_Important_Notes.md#133-service-information)

- [II. HTTP Communication](02_HTTP_Basics.md)
  - [2.1 Basic Description](02_HTTP_Basics.md#21-basic-description)
  - [2.2 Error Handling](02_HTTP_Basics.md#22-error-handling)
  - [2.3 Authentication Methods](02_HTTP_Basics.md#23-authentication-methods)
  - [2.4 Authentication Interface](02_HTTP_Basics.md#24-authentication-interface)
  - [2.5 Data Read/Write Interface](03_HTTP_Data_Read_Write.md)
    - [2.5.1 Direct Read/Write](03_HTTP_Data_Read_Write.md#251-direct-readwrite)
    - [2.5.2 Cache Read](03_HTTP_Data_Read_Write.md#252-cache-read)
  - [2.6 File Management Interface](04_HTTP_File_Management.md)
  - [2.7 Data Analysis Interface](05_HTTP_Data_Analysis.md)
  - [2.8 Historical Data Interface](06_HTTP_Historical_Data.md)
  - [2.9 Gateway Configuration Interface](07_HTTP_Gateway_Config.md)
  - [2.10 Gateway Function Interface](08_HTTP_Gateway_Functions.md)

- [III. MODBUS Communication](09_MODBUS_Communication.md)
  - [3.1 Machine-Related Data](09_MODBUS_Communication.md#31-machine-related-data)
  - [3.2 Machine Group-Related Data](09_MODBUS_Communication.md#32-machine-group-related-data)

- [IV. MQTT Communication](10_MQTT_Communication.md)
  - [4.1 Data Report Message Format](10_MQTT_Communication.md#41-data-report-message-format)
  - [4.2 RPC Interface](10_MQTT_Communication.md#42-rpc-interface)

- [V. Database Communication](11_Database_Communication.md)

- [VI. Simulated Machine Test](12_Simulation_and_FAQ.md#vi-simulated-machine-test)

- [VII. Frequently Asked Questions](12_Simulation_and_FAQ.md#vii-frequently-asked-questions)

- [Version Change History](13_Version_History.md)

---

## Table Index

| Table No. | Table Name | File |
|-----------|-----------|------|
| Table 1 | Data Classes | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 2 | Alarm History \<field\> Data Fields | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 3 | Current Alarm \<field\> Data Fields | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 4 | Servo Axis Overload \<field\> Data Fields | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 5 | Servo Axis Overload \<tag\> Data Tags | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 6 | Machine Status \<field\> Data Fields | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 7 | Machine Status \<tag\> Data Tags | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 8 | Machining Count \<field\> Data Fields | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 10 | Current Tool Number \<field\> Data Fields | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 11 | Current Tool Number \<tag\> Data Tags | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 12 | Cycle Time \<field\> Data Fields | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 13 | Energy Consumption \<field\> Data Fields | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 14 | Feed Data \<field\> Data Fields | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 15 | Feed Data \<tag\> Data Tags | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 16 | Feed Override \<field\> Data Fields | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 17 | Feed Override \<tag\> Data Tags | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 18 | Machine Group Machining Count \<field\> Data Fields | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 19 | Machine Group Cumulative Status Time \<field\> Data Fields | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 20 | Machine Group OEE \<field\> Data Fields | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 21 | Laser Power \<field\> Data Fields | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 22 | Load Data \<field\> Data Fields | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 23 | Load Data \<tag\> Data Tags | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 24 | Log History \<field\> Data Fields | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 25 | Machine OEE \<field\> Data Fields | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 26 | Tool Offset Data \<field\> Data Fields | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 27 | Tool Offset Data \<tag\> Data Tags | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 28 | Tool Offset Data Tag Combinations | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 29 | PLC Data \<field\> Data Fields | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 30 | PLC Data \<tag\> Data Tags | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 31 | Coordinate Data \<field\> Data Fields | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 32 | Coordinate Data \<tag\> Data Tags | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 33 | Program Block \<field\> Data Fields | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 34 | Program Block \<tag\> Data Tags | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 35 | Current Program \<field\> Data Fields | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 36 | Current Program \<tag\> Data Tags | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 37 | Spindle Overload \<field\> Data Fields | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 38 | Spindle Overload \<tag\> Data Tags | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 39 | Machine Time Data \<field\> Data Fields | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 40 | Time Data Supported by Each CNC Model | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 41 | Tool Life Data \<field\> Data Fields | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 42 | Tool Life Data \<tag\> Data Tags | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 43 | Tool Life Data Tag Combinations | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 44 | Tool Life Types | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 45 | Running Status | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 46 | Alarm Status | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 47 | Emergency Stop Status | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 48 | Dry Run Status | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 49 | Alarm Level | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 50 | Gateway Service Running Status | [01_Important_Notes.md](01_Important_Notes.md) |
| Table 51 | Common Error Codes | [02_HTTP_Basics.md](02_HTTP_Basics.md) |
| Table 52 | PLC Data Common Areas and Parameters | [03_HTTP_Data_Read_Write.md](03_HTTP_Data_Read_Write.md) |
| Table 53 | PLC Data Special Areas and Parameters | [03_HTTP_Data_Read_Write.md](03_HTTP_Data_Read_Write.md) |
| Table 54 | PLC Address Numeral System Prefixes | [03_HTTP_Data_Read_Write.md](03_HTTP_Data_Read_Write.md) |
| Table 55 | PLC Data Types | [03_HTTP_Data_Read_Write.md](03_HTTP_Data_Read_Write.md) |
| Table 56 | Default Root Directory Paths by CNC Model | [04_HTTP_File_Management.md](04_HTTP_File_Management.md) |
| Table 57 | Support for Sub-folder Listing in readProgramList by CNC Model | [04_HTTP_File_Management.md](04_HTTP_File_Management.md) |
| Table 58 | CNC Model Support for Create/Delete Machine Directory | [04_HTTP_File_Management.md](04_HTTP_File_Management.md) |
| Table 59 | API Command Format | [07_HTTP_Gateway_Config.md](07_HTTP_Gateway_Config.md) |
| Table 60 | Machine Configuration Parameters | [07_HTTP_Gateway_Config.md](07_HTTP_Gateway_Config.md) |
| Table 61 | machineType Corresponding Machine Types | [07_HTTP_Gateway_Config.md](07_HTTP_Gateway_Config.md) |
| Table 62 | Machine Group Configuration Parameters | [07_HTTP_Gateway_Config.md](07_HTTP_Gateway_Config.md) |
| Table 63 | machines — Machine List Information | [07_HTTP_Gateway_Config.md](07_HTTP_Gateway_Config.md) |
| Table 64 | Machine Task Interval Configuration Parameters | [07_HTTP_Gateway_Config.md](07_HTTP_Gateway_Config.md) |
| Table 65 | Machine Group Task Interval Configuration Parameters | [07_HTTP_Gateway_Config.md](07_HTTP_Gateway_Config.md) |
| Table 66 | OEE Monitoring Configuration Parameters | [07_HTTP_Gateway_Config.md](07_HTTP_Gateway_Config.md) |
| Table 67 | monitorMode — Monitoring Mode | [07_HTTP_Gateway_Config.md](07_HTTP_Gateway_Config.md) |
| Table 68 | availabilityMode — Availability Calculation Method | [07_HTTP_Gateway_Config.md](07_HTTP_Gateway_Config.md) |
| Table 69 | Tool Life Monitoring Configuration Parameters | [07_HTTP_Gateway_Config.md](07_HTTP_Gateway_Config.md) |
| Table 70 | Machining Count Monitoring Configuration Parameters | [07_HTTP_Gateway_Config.md](07_HTTP_Gateway_Config.md) |
| Table 71 | Overload Monitoring Configuration Parameters | [07_HTTP_Gateway_Config.md](07_HTTP_Gateway_Config.md) |
| Table 72 | Alarm Monitoring Configuration Parameters | [07_HTTP_Gateway_Config.md](07_HTTP_Gateway_Config.md) |
| Table 73 | minimumAlarmLevel — Minimum Alarm Level | [07_HTTP_Gateway_Config.md](07_HTTP_Gateway_Config.md) |
| Table 74 | Machine Status Monitoring Configuration Parameters | [07_HTTP_Gateway_Config.md](07_HTTP_Gateway_Config.md) |
| Table 75 | Cloud Platform Configuration Parameters | [07_HTTP_Gateway_Config.md](07_HTTP_Gateway_Config.md) |
| Table 76 | MODBUS Configuration Parameters | [07_HTTP_Gateway_Config.md](07_HTTP_Gateway_Config.md) |
| Table 77 | MQTT Configuration Parameters | [07_HTTP_Gateway_Config.md](07_HTTP_Gateway_Config.md) |
| Table 78 | MQTT mode — Mode | [07_HTTP_Gateway_Config.md](07_HTTP_Gateway_Config.md) |
| Table 79 | Database Configuration Parameters | [07_HTTP_Gateway_Config.md](07_HTTP_Gateway_Config.md) |
| Table 80 | Database Types | [07_HTTP_Gateway_Config.md](07_HTTP_Gateway_Config.md) |
| Table 81 | Database Save Modes | [07_HTTP_Gateway_Config.md](07_HTTP_Gateway_Config.md) |
| Table 82 | Gateway File Server Configuration Parameters | [07_HTTP_Gateway_Config.md](07_HTTP_Gateway_Config.md) |
| Table 83 | HTTP Configuration Parameters | [07_HTTP_Gateway_Config.md](07_HTTP_Gateway_Config.md) |
| Table 84 | Hub Configuration Parameters | [07_HTTP_Gateway_Config.md](07_HTTP_Gateway_Config.md) |
| Table 85 | Remote Access Configuration Parameters | [07_HTTP_Gateway_Config.md](07_HTTP_Gateway_Config.md) |
