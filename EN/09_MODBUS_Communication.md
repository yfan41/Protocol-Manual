## III. MODBUS Communication

Once MODBUS communication is enabled and the automatic data collection task for the target machine or machine group is activated (see the *User Manual*, section 5.3.1 Add Machine — task settings for each machine type, and section 5.4.1.2 Machine Group Task Settings), the gateway will store the collected task result data at the corresponding MODBUS addresses based on the MODBUS protocol. The MODBUS communication address is the \<gateway IP\>, and the port is 502. When using MODBUS communication, the last octet of the machine's IP address is used as the slave station ID. For example, if the machine IP is 192.168.100.2, the corresponding slave station ID is 2. When using MODBUS, the last octet of the machine IP must not be 0 (0 is reserved for the gateway), and each machine connected to the same gateway must have a unique last octet; otherwise, data identification errors will occur. Refer to *User Manual* section 5.6.2 MODBUS Configuration for instructions on configuring the MODBUS service.

**Note: The gateway MODBUS server uses 0-based addressing. If the client uses 1-based addressing, add 1 to the corresponding address values.**

---

### 3.1 Machine-Related Data

MODBUS data addresses are as follows:

| Address | Name | Data Type | Unit | Notes |
|---------|------|-----------|------|-------|
| 400010 | Connection Status | UInt16 | – | 1=Connected; 2=Disconnected; 3=Error |
| 400011 | Alarm Count | UInt16 | items | Current number of alarms |
| 400012 | CNC Running Status | Int16 | – | Current status code; see section 5.1 Machine Status |
| 400013 | Cumulative Alarm Count | UInt32 | items | |
| 400015 | Cumulative Alarm Time | UInt32 | seconds | |
| 400040–400075 | Axis Names, Axes 1–18 | String(4) | – | Converted to a 4-byte array (2 address slots) using the encoding set in MODBUS configuration. |
| 400100 | Machining Count | UInt32 | pieces | Machining count retrieved from the machine system |
| 400102 | Spindle Override | Float | % | Spindle speed override |
| 400104 | Spindle Speed | Float | Same as machine setting | |
| 400106 | Feed Override | Float | % | |
| 400108 | Feed Rate | Float | Same as machine setting | |
| 400110–400115 | Spindle 1–3 Load | Float | % | |
| 400120 | Rapid Feed Override | Float | % | |
| 400172 | Cumulative Power-On Time 1 | Int32 | minutes | Time calculation details: see section 1.2.25 ToolLife. A value of -2147483648 indicates the machine model does not support this time data. |
| 400174 | Cumulative Power-On Time 2 | Int32 | milliseconds | |
| 400176 | Current Power-On Time 1 | Int32 | minutes | |
| 400178 | Current Power-On Time 2 | Int32 | milliseconds | |
| 400180 | Cumulative Running Time 1 | Int32 | minutes | |
| 400182 | Cumulative Running Time 2 | Int32 | milliseconds | |
| 400184 | Current Running Time 1 | Int32 | minutes | |
| 400186 | Current Running Time 2 | Int32 | milliseconds | |
| 400188 | Cumulative Machining Time 1 | Int32 | minutes | |
| 400190 | Cumulative Machining Time 2 | Int32 | milliseconds | |
| 400192 | Current Machining Time 1 | Int32 | minutes | |
| 400194 | Current Machining Time 2 | Int32 | milliseconds | |
| 400196 | Last Cycle Time 1 | Int32 | minutes | |
| 400198 | Last Cycle Time 2 | Int32 | milliseconds | |
| 400200 | Current Cycle Time 1 | Int32 | minutes | |
| 400202 | Current Cycle Time 2 | Int32 | milliseconds | |
| 400204 | Current Cutting Time 1 | Int32 | minutes | |
| 400206 | Current Cutting Time 2 | Int32 | milliseconds | |
| 400208 | Remaining Cycle Time 1 | Int32 | minutes | |
| 400210 | Remaining Cycle Time 2 | Int32 | milliseconds | |
| 400220 | Current Tool Number | String(16) | – | Converted to a 16-byte array (8 address slots) using the encoding set in MODBUS configuration. |
| 400228 | Running Program Name | String(32) | – | Currently executing sub-program name |
| 400244 | Main Program Name | String(32) | – | Current main program name |
| 400260 | Running Program Block Sequence No. | String(16) | – | Sequence number of the currently executing program block |
| 400268 | Current Program Block | String(70) | – | Content of the currently running program block |
| 400498 | Current Machining Count | UInt32 | pieces | Machining count within the current monitoring period |
| 400500 | Cumulative Machining Count | UInt32 | pieces | |
| 400502 | Off Time | UInt32 | seconds | |
| 400504 | Emergency Stop Time | UInt32 | seconds | |
| 400506 | Wait Time | UInt32 | seconds | |
| 400508 | Auto Run Time | UInt32 | seconds | |
| 400510 | Setup Time | UInt32 | seconds | |
| 400512 | Machine Monitoring Availability | Float | % | |
| 400520 | Cumulative Off Time | UInt32 | seconds | |
| 400522 | Cumulative Emergency Stop Time | UInt32 | seconds | |
| 400524 | Cumulative Wait Time | UInt32 | seconds | |
| 400526 | Cumulative Auto Run Time | UInt32 | seconds | |
| 400528 | Cumulative Setup Time | UInt32 | seconds | |
| 400546 | Last Cycle Time (gateway-tracked) | UInt32 | seconds | |
| 400600 | Program Stack | String(128) | – | Converted to a 128-byte array (64 address slots) using the encoding set in MODBUS configuration. |
| 400664 | Current Tool Offset Number | String(16) | – | |
| 400900 | Preset Power | Float | % | Laser cutting machine preset power |
| 400902 | Actual Power | Float | % | Laser cutting machine actual power |
| 401100–401135 | Axis 1–18 Load | Float | % | |
| 401136–401171 | Axis 1–18 Machine Coordinate | Float | Same as machine setting | |
| 401172–401207 | Axis 1–18 Relative Coordinate | Float | Same as machine setting | |
| 401208–401243 | Axis 1–18 Remaining Distance | Float | Same as machine setting | |
| 401244–401279 | Axis 1–18 Absolute Coordinate | Float | Same as machine setting | |
| 401400–401405 | Spindle 1–3 Cumulative Overload Time | UInt32 | milliseconds | |
| 401410–401445 | Axis 1–18 Cumulative Overload Time | UInt32 | milliseconds | |
| 405000 | Alarm 1 | String(200) | – | Contains alarm content, time, and level |
| 405100 | Alarm 2 | String(200) | – | Same as above |
| 405200 | Alarm 3 | String(200) | – | Same as above |
| 405300 | Alarm 4 | String(200) | – | Same as above |
| 405400 | Alarm 5 | String(200) | – | Same as above |
| 420000–429999 | PLC Data (positions 1–10000) | UInt16 | – | PLC task data followed by external PLC task data, arranged in task order. 32-bit, 64-bit, and String type data are converted to UInt16 using the method set in MODBUS configuration. |

**Note 1:** Cumulative alarm count and cumulative machining count are totals accumulated since the gateway started collecting data from the machine. These values are stored on the gateway, bound to the machineID (the last two octets of the machine IP), and are never reset.

**Note 2:** Addresses 400502–400510 represent the machine status times within the monitoring period. Address 400512 is the machine availability within the monitoring period. See *User Manual* section 6.1.1 Machine OEE Data.

**Note 3:** String(200) indicates that the string is converted to a 200-byte array using the configured encoding (see *User Manual* section 5.6.2 MODBUS Configuration), which corresponds to 100 MODBUS address slots (UInt16).

**Note 4:** The last cycle time (address 400546) is monitored by the gateway's machine cycle time data task. If the first cycle after a gateway restart has not yet completed, this value is 0. The definition of "last cycle time" is the same as "last cycle time" from the machine, but the former is tracked by the gateway and is supported by all machines that can track production count and status. The latter is provided by the machine itself and is only supported by some machine models.

---

### 3.2 Machine Group-Related Data

Machine group-related data is stored in slave station 0. Data addresses are as follows:

| Address | Name | Data Type | Unit | Notes |
|---------|------|-----------|------|-------|
| 400100 | Machine Group 1 Cumulative Machining Count | UInt32 | pieces | |
| 400102 | Current Number of Machines in Group 1 — Off | Int16 | machines | |
| 400103 | Current Number of Machines in Group 1 — Emergency Stop | Int16 | machines | |
| 400104 | Current Number of Machines in Group 1 — Waiting | Int16 | machines | |
| 400105 | Current Number of Machines in Group 1 — Auto Running | Int16 | machines | |
| 400106 | Current Number of Machines in Group 1 — Setup | Int16 | machines | |
| 400110 | Machine Group 1 Off Time | UInt32 | seconds | |
| 400112 | Machine Group 1 Emergency Stop Time | UInt32 | seconds | |
| 400114 | Machine Group 1 Wait Time | UInt32 | seconds | |
| 400116 | Machine Group 1 Auto Run Time | UInt32 | seconds | |
| 400118 | Machine Group 1 Setup Time | UInt32 | seconds | |
| 400120 | Machine Group 1 Monitoring Availability | Float | % | |
| 400130 | Machine Group 1 Cumulative Off Time | UInt32 | seconds | |
| 400132 | Machine Group 1 Cumulative Emergency Stop Time | UInt32 | seconds | |
| 400134 | Machine Group 1 Cumulative Wait Time | UInt32 | seconds | |
| 400136 | Machine Group 1 Cumulative Auto Run Time | UInt32 | seconds | |
| 400138 | Machine Group 1 Cumulative Setup Time | UInt32 | seconds | |
| 400200–400299 | Machine Group 2 (all addresses = Group 1 address + 100) | | | |
| … | | | | |
| 401600–401699 | Machine Group 16 (all addresses = Group 1 address + 1500) | | | |

**Note 1:** Address 400100 Cumulative Machining Count for the machine group is the total accumulated since the gateway started collecting data for the group. See *User Manual* section 6.1.3 Machine Group Machining Count.

**Note 2:** Addresses 400110–400118 represent the sum of status times across all active machines in the group within the monitoring period. Address 400120 is the machine group monitoring availability within the monitoring period. See *User Manual* section 6.1.2 Machine Group OEE Data.
