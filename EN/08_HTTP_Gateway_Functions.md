## 2.10 Gateway Function Interfaces

The gateway function interfaces are used to command the gateway to perform specific functions, including the Core service function interfaces and the Gateway service function interfaces.

---

### 2.10.1 Core Service Function Interfaces

Base URL: `/api/core`.

---

#### 2.10.1.1 info — Get Core Service Information

No request parameters.

| Method | URL |
|--------|-----|
| GET | `/api/core/info` |

**Response Example**

```json
{
  "version": "1.19.4.18"
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| version | String | (Required) Version number |

---

#### 2.10.1.2 license-info — Get License Information

Retrieves license details including expiration date, license count, license type, and license status. No request parameters.

| Method | URL |
|--------|-----|
| GET | `/api/core/license-info` |

**Response Example**

```json
{
  "isValid": true,
  "license": {
    "company": "Bivrost",
    "product": "IoT Gateway",
    "machineCount": 255,
    "plcCount": 40,
    "robotCount": 60,
    "cncCount": 150,
    "laserCount": 5,
    "featureAppDNC": true,
    "licType": "Full",
    "uid": "XXXXXX-XXXXXX-XXXXXX-XXXXXX",
    "expiration": "2999-12-31T23:59:59"
  }
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| isValid | Bool | (Required) Whether the license is valid. `true` = valid, `false` = invalid. |
| license | object | (Required) License details |
| company | String | Name of the company that issued the license. Not returned in generic editions. |
| product | String | (Required) Product name for this license. |
| machineCount | Int32 | (Required) Total license count. |
| plcCount | Int32 | PLC license count. If not returned, the PLC license count equals the total license count. |
| robotCount | Int32 | Robot license count. If not returned, the robot license count equals the total license count. |
| cncCount | Int32 | CNC license count. If not returned, the CNC license count equals the total license count. |
| laserCount | Int32 | Laser machine license count. If not returned, the laser license count equals the total license count. |
| featureAppDNC | Bool | (Required) Whether the DNC File Transfer Pro feature is enabled. `true` = enabled, `false` = not enabled. |
| licType | String | (Required) License type: `Basic`, `Standard`, `Full`, `DNC`, `PLC`. |
| uid | String | (Required) Gateway UID this license is bound to. |
| expiration | String | (Required) License expiration date/time. |

---

#### 2.10.1.3 service-status — Get Service Status

Retrieves the status of services including cloud platform, MODBUS, MQTT, database, and local cache. No request parameters.

| Method | URL |
|--------|-----|
| GET | `/api/core/service-status` |

**Response Example**

```json
{
  "mqtt": 3,
  "queueSizeMqtt": 9,
  "modbus": 0,
  "queueSizeModbus": 0,
  "tbCloud": 0,
  "queueSizeTbCloud": 0,
  "localDB": 3,
  "queueSizeLocalDB": 0,
  "writeData": 2,
  "queueSizeWriteData": 0,
  "localDBLog": 0,
  "queueSizeLocalDBLog": 0,
  "hubBroadcasting": 2,
  "queueSizeHubBroadcasting": 0,
  "tbHubBroadcasting": 0,
  "queueSizeTbHubBroadcasting": 0,
  "needRestart": false
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| mqtt | Int32 | (Required) MQTT status. Refer to Table 49 — Service Running Status. |
| queueSizeMqtt | Int32 | (Required) MQTT queue length |
| modbus | Int32 | (Required) MODBUS status. Refer to Table 49 — Service Running Status. |
| queueSizeModbus | Int32 | (Required) MODBUS queue length |
| tbCloud | Int32 | (Required) Cloud platform status. Refer to Table 49 — Service Running Status. |
| queueSizeTbCloud | Int32 | (Required) Cloud platform queue length |
| localDB | Int32 | (Required) Local cache status. Refer to Table 49 — Service Running Status. |
| queueSizeLocalDB | Int32 | (Required) Local cache queue length |
| writeData | Int32 | (Required) Database status. Refer to Table 49 — Service Running Status. |
| queueSizeWriteData | Int32 | (Required) Database queue length |
| localDBLog | Int32 | (Required) Log database status. Refer to Table 49 — Service Running Status. |
| queueSizeLocalDBLog | Int32 | (Required) Log database queue length |
| hubBroadcasting | Int32 | (Required) Hub broadcasting status. Refer to Table 49 — Service Running Status. |
| queueSizeHubBroadcasting | Int32 | (Required) Hub broadcasting queue length |
| tbHubBroadcasting | Int32 | (Required) Cloud platform broadcasting status. Refer to Table 49 — Service Running Status. |
| queueSizeTbHubBroadcasting | Int32 | (Required) Cloud platform broadcasting queue length |
| needRestart | Bool | (Required) Whether a service restart is required to apply settings |

---

#### 2.10.1.4 upload-license — Upload Gateway License

Uploads a gateway license as a string stream.

| Method | URL |
|--------|-----|
| POST | `/api/core/upload-license` |

**Request Body Example**

```json
{
  "Base64": "string"
}
```

> The gateway license file should be converted to a Base64 string before uploading. The gateway reads the file content in Base64 string format.

**Response Example**

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| errorCode | Int32 | (Required) Error code. `0` = success. |
| errorMsg | String | (Required) Error message |

---

### 2.10.2 Gateway Service Function Interfaces

Base URL: `/api/gateway`.

---

#### 2.10.2.1 alias — Get Gateway Name

No request parameters.

| Method | URL |
|--------|-----|
| GET | `/api/gateway/alias` |

**Response Example**

```json
{
  "alias": "iotgw"
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| alias | String | (Required) Gateway name |

---

#### 2.10.2.2 update-alias — Update Gateway Name

No request parameters. Note: this change takes effect after a hardware reboot.

| Method | URL |
|--------|-----|
| POST | `/api/gateway/update-alias` |

**Request Body Example** (application/json)

```json
{
  "alias": "newAlias"
}
```

| Request Parameter | Type | Description |
|-------------------|------|-------------|
| alias | String | (Required) New gateway name |

**Response Example**

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| errorCode | Int32 | (Required) Error code. `0` = success. |
| errorMsg | String | (Required) Error message |

---

#### 2.10.2.3 reboot — Hardware Reboot

Equivalent to clicking "Power" → "Restart" on the Gateway Management page. No request parameters.

| Method | URL |
|--------|-----|
| GET | `/api/gateway/reboot` |

**Response Example**

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| errorCode | Int32 | (Required) Error code. `0` = success. |
| errorMsg | String | (Required) Error message |

---

#### 2.10.2.4 restart — Restart All Services

Restarts both the Core service and the Gateway service. This is different from the "Restart Service" button on the gateway home page. No request parameters.

| Method | URL |
|--------|-----|
| GET | `/api/gateway/restart` |

**Response Example**

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| errorCode | Int32 | (Required) Error code. `0` = success. |
| errorMsg | String | (Required) Error message |

---

#### 2.10.2.5 restart-service — Restart Core Service

Use this interface after modifying machine configuration, machine group configuration, task configuration, or communication configuration. Equivalent to clicking the "Restart Service" button on the gateway home page. No request parameters.

| Method | URL |
|--------|-----|
| GET | `/api/gateway/restart-service` |

**Response Example**

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| errorCode | Int32 | (Required) Error code. `0` = success. |
| errorMsg | String | (Required) Error message |

---

#### 2.10.2.6 shut-down — Gateway Shutdown

Equivalent to clicking "Power" → "Shut Down" on the Gateway Management page. No request parameters.

| Method | URL |
|--------|-----|
| GET | `/api/gateway/shut-down` |

**Response Example**

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| errorCode | Int32 | (Required) Error code. `0` = success. |
| errorMsg | String | (Required) Error message |

---

#### 2.10.2.7 internet-connection — Get Gateway Internet Status

Retrieves whether the gateway is connected to the internet. No request parameters.

| Method | URL |
|--------|-----|
| GET | `/api/gateway/internet-connection` |

**Response Example**

```json
{
  "isOnline": true
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| isOnline | Bool | (Required) Internet connectivity status. `true` = connected, `false` = not connected. |

---

#### 2.10.2.8 time — Get Gateway Current Time

No request parameters.

| Method | URL |
|--------|-----|
| GET | `/api/gateway/time` |

**Response Example**

```json
{
  "localTime": "2025-06-30T13:51:19.2864043+08:00"
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| localTime | String | (Required) Gateway local time (ISO 8601) |

---

#### 2.10.2.9 sync-time — Synchronize Gateway Time

| Method | URL |
|--------|-----|
| GET | `/api/gateway/sync-time` |

| Request Parameter | Type | Description |
|-------------------|------|-------------|
| timeServerAddress | String | (Required) NTP server address. Multiple addresses separated by `";"`. The gateway will first attempt to sync with the first address; if that fails, it will try the next. Example: `ntp1.aliyun.com;ntp2.aliyun.com;ntp3.aliyun.com;ntp4.aliyun.com;` |

**Response Example**

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| errorCode | Int32 | (Required) Error code. `0` = success. |
| errorMsg | String | (Required) Error message |

---

#### 2.10.2.10 time-zone — Get Gateway Time Zone

No request parameters.

| Method | URL |
|--------|-----|
| GET | `/api/gateway/time-zone` |

**Response Example**

```json
{
  "timeZoneID": "China Standard Time"
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| timeZoneID | String | (Required) Gateway time zone (Microsoft Windows Time Zone ID) |

---

#### 2.10.2.11 time-zones — Get Available Time Zones

No request parameters.

| Method | URL |
|--------|-----|
| GET | `/api/gateway/time-zones` |

**Response Example**

```json
{
  "timeZoneIDs": [
    "Dateline Standard Time",
    "UTC-11",
    "Hawaiian Standard Time",
    "Aleutian Standard Time",
    ...
    "China Standard Time",
    ...
  ]
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| timeZoneIDs | String[] | (Required) Available time zones (Microsoft Windows Time Zone IDs) |

---

#### 2.10.2.12 update-time-zone — Update Gateway Time Zone

| Method | URL |
|--------|-----|
| POST | `/api/gateway/update-time-zone` |

**Request Body Example** (application/json)

```json
{
  "timeZoneID": "China Standard Time"
}
```

| Request Parameter | Type | Description |
|-------------------|------|-------------|
| timeZoneID | String | (Required) Gateway time zone (Microsoft Windows Time Zone ID). Available options can be obtained from section 2.10.2.11 time-zones. |

**Response Example**

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| errorCode | Int32 | (Required) Error code. `0` = success. |
| errorMsg | String | (Required) Error message |

---

#### 2.10.2.13 network-adapters — Get Network Adapter List

No request parameters.

| Method | URL |
|--------|-----|
| GET | `/api/gateway/network-adapters` |

**Response Example**

```json
{
  "names": [
    "LAN1",
    "LAN2",
    "WLAN"
  ]
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| names | String[] | (Required) Network adapter names |

---

#### 2.10.2.14 lan — Get Wired Network Settings

| Method | URL |
|--------|-----|
| GET | `/api/gateway/lan` |

| Request Parameter | Type | Description |
|-------------------|------|-------------|
| name | String | (Required) Network adapter name. Values: `LAN1` or `LAN2`. |

**Response Example**

```json
{
  "name": "LAN1",
  "macAddress": "00:E0:71:BC:D2:53",
  "state": "Disconnected",
  "isDHCPEnabled": false,
  "ipAddress": "192.168.100.1",
  "subMask": "255.255.0.0",
  "defaultGateway": "",
  "isDNSServerDHCPEnabled": false,
  "dnsServer1": "",
  "dnsServer2": ""
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| name | String | (Required) Network adapter name. Values: `LAN1` or `LAN2`. |
| macAddress | String | (Required) Physical (MAC) address |
| state | String | (Required) State. Values: `Connected`, `Disconnected`. |
| isDHCPEnabled | Bool | (Required) Obtain IP address automatically (DHCP) |
| ipAddress | String | (Required) IP address |
| subMask | String | (Required) Subnet mask |
| defaultGateway | String | (Required) Default gateway |
| isDNSServerDHCPEnabled | Bool | (Required) Obtain DNS server address automatically |
| dnsServer1 | String | (Required) Preferred DNS server |
| dnsServer2 | String | (Required) Alternate DNS server |

---

#### 2.10.2.15 update-lan — Update Wired Network Settings

| Method | URL |
|--------|-----|
| POST | `/api/gateway/update-lan` |

**Request Body Example** (application/json)

```json
{
  "name": "LAN1",
  "isDHCPEnabled": false,
  "ipAddress": "192.168.100.1",
  "subMask": "255.255.0.0",
  "defaultGateway": "",
  "isDNSServerDHCPEnabled": false,
  "dnsServer1": "",
  "dnsServer2": ""
}
```

| Request Parameter | Type | Description |
|-------------------|------|-------------|
| name | String | (Required) Target network adapter name. Values: `LAN1` or `LAN2`. |
| isDHCPEnabled | Bool | Obtain IP address automatically (DHCP) |
| ipAddress | String | IP address |
| subMask | String | Subnet mask |
| defaultGateway | String | Default gateway |
| isDNSServerDHCPEnabled | Bool | Obtain DNS server address automatically |
| dnsServer1 | String | Preferred DNS server |
| dnsServer2 | String | Alternate DNS server |

**Response Example**

```json
{
  "name": "LAN1",
  "macAddress": "00:E0:71:BC:D2:53",
  "state": "Disconnected",
  "isDHCPEnabled": false,
  "ipAddress": "192.168.100.1",
  "subMask": "255.255.0.0",
  "defaultGateway": "",
  "isDNSServerDHCPEnabled": false,
  "dnsServer1": "",
  "dnsServer2": ""
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| name | String | (Required) Network adapter name. Values: `LAN1` or `LAN2`. |
| macAddress | String | (Required) Physical (MAC) address |
| state | String | (Required) State. Values: `Connected`, `Disconnected`. |
| isDHCPEnabled | Bool | (Required) Obtain IP address automatically (DHCP) |
| ipAddress | String | (Required) IP address |
| subMask | String | (Required) Subnet mask |
| defaultGateway | String | (Required) Default gateway |
| isDNSServerDHCPEnabled | Bool | (Required) Obtain DNS server address automatically |
| dnsServer1 | String | (Required) Preferred DNS server |
| dnsServer2 | String | (Required) Alternate DNS server |

---

#### 2.10.2.16 wifi — Get Wi-Fi Settings

No request parameters.

| Method | URL |
|--------|-----|
| GET | `/api/gateway/wifi` |

**Response Example**

```json
{
  "wifiName": "myWifi-5G",
  "signalStrength": 99,
  "macAddress": "00:A0:71:BD:E2:63",
  "state": "Connected",
  "isDHCPEnabled": true,
  "ipAddress": "192.168.1.88",
  "subMask": "255.255.255.0",
  "defaultGateway": "192.168.1.1",
  "isDNSServerDHCPEnabled": true,
  "dnsServer1": "192.168.211.1",
  "dnsServer2": "8.8.8.8"
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| wifiName | String | (Required) Wi-Fi SSID |
| signalStrength | Int32 | Range: 0–100. Higher value = stronger signal. |
| macAddress | String | (Required) Physical (MAC) address |
| state | String | (Required) State. Values: `Connected`, `Disconnected`. |
| isDHCPEnabled | Bool | (Required) Obtain IP address automatically (DHCP) |
| ipAddress | String | (Required) IP address |
| subMask | String | (Required) Subnet mask |
| defaultGateway | String | (Required) Default gateway |
| isDNSServerDHCPEnabled | Bool | (Required) Obtain DNS server address automatically |
| dnsServer1 | String | (Required) Preferred DNS server |
| dnsServer2 | String | (Required) Alternate DNS server |

---

#### 2.10.2.17 update-wifi — Update Wi-Fi Settings

| Method | URL |
|--------|-----|
| POST | `/api/gateway/update-wifi` |

**Request Body Example** (application/json)

```json
{
  "isDHCPEnabled": true,
  "ipAddress": "192.168.1.88",
  "subMask": "255.255.255.0",
  "defaultGateway": "192.168.1.1",
  "isDNSServerDHCPEnabled": true,
  "dnsServer1": "192.168.211.1",
  "dnsServer2": "8.8.8.8"
}
```

| Request Parameter | Type | Description |
|-------------------|------|-------------|
| isDHCPEnabled | Bool | Obtain IP address automatically (DHCP) |
| ipAddress | String | IP address |
| subMask | String | Subnet mask |
| defaultGateway | String | Default gateway |
| isDNSServerDHCPEnabled | Bool | Obtain DNS server address automatically |
| dnsServer1 | String | Preferred DNS server |
| dnsServer2 | String | Alternate DNS server |

**Response Example**

```json
{
  "wifiName": "myWifi-5G",
  "signalStrength": 99,
  "macAddress": "00:A0:71:BD:E2:63",
  "state": "Connected",
  "isDHCPEnabled": true,
  "ipAddress": "192.168.1.88",
  "subMask": "255.255.255.0",
  "defaultGateway": "192.168.1.1",
  "isDNSServerDHCPEnabled": true,
  "dnsServer1": "192.168.211.1",
  "dnsServer2": "8.8.8.8"
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| wifiName | String | (Required) Wi-Fi SSID |
| signalStrength | Int32 | Range: 0–100. Higher value = stronger signal. |
| macAddress | String | (Required) Physical (MAC) address |
| state | String | (Required) State. Values: `Connected`, `Disconnected`. |
| isDHCPEnabled | Bool | (Required) Obtain IP address automatically (DHCP) |
| ipAddress | String | (Required) IP address |
| subMask | String | (Required) Subnet mask |
| defaultGateway | String | (Required) Default gateway |
| isDNSServerDHCPEnabled | Bool | (Required) Obtain DNS server address automatically |
| dnsServer1 | String | (Required) Preferred DNS server |
| dnsServer2 | String | (Required) Alternate DNS server |

---

#### 2.10.2.18 search-wifi — Scan for Wi-Fi Networks

No request parameters.

| Method | URL |
|--------|-----|
| GET | `/api/gateway/search-wifi` |

**Response Example**

```json
[
  {
    "wifiName": "myWifi-5G",
    "signalStrength": 99,
    "state": "Connected"
  },
  {
    "wifiName": "myWifi-2.4G",
    "signalStrength": 99,
    "state": "Disconnected"
  }
]
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| wifiName | String | (Required) Wi-Fi SSID |
| signalStrength | Int32 | Range: 0–100. Higher value = stronger signal. |
| state | String | (Required) State. Values: `Connected`, `Disconnected`. |

---

#### 2.10.2.19 connect-wifi — Connect to Wi-Fi

| Method | URL |
|--------|-----|
| GET | `/api/gateway/connect-wifi` |

| Request Parameter | Type | Description |
|-------------------|------|-------------|
| wifiName | String | (Required) Target Wi-Fi SSID |
| wifiPassword | String | Target Wi-Fi password |

**Response Example**

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| errorCode | Int32 | (Required) Error code. `0` = success. |
| errorMsg | String | (Required) Error message |

---

#### 2.10.2.20 disconnect-wifi — Disconnect from Wi-Fi

No request parameters.

| Method | URL |
|--------|-----|
| GET | `/api/gateway/disconnect-wifi` |

**Response Example**

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| errorCode | Int32 | (Required) Error code. `0` = success. |
| errorMsg | String | (Required) Error message |

---

#### 2.10.2.21 static-routing — Get Static Routing Settings

No request parameters.

| Method | URL |
|--------|-----|
| GET | `/api/gateway/static-routing` |

**Response Example**

```json
{
  "staticRouting": "0.0.0.0, 0.0.0.0, 192.168.100.1;"
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| staticRouting | String | (Required) Static routing configuration string |

---

#### 2.10.2.22 update-static-routing — Update Static Routing Settings

| Method | URL |
|--------|-----|
| POST | `/api/gateway/update-static-routing` |

**Request Body Example** (application/json)

```json
{
  "staticRouting": "0.0.0.0, 0.0.0.0, 192.168.100.1;"
}
```

| Request Parameter | Type | Description |
|-------------------|------|-------------|
| staticRouting | String | (Required) Static routing configuration string |

**Response Example**

```json
{
  "staticRouting": "0.0.0.0, 0.0.0.0, 192.168.100.1;"
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| staticRouting | String | (Required) Static routing configuration string |

---

#### 2.10.2.23 connect-remote-host — Connect to Remote Server

No request parameters.

| Method | URL |
|--------|-----|
| GET | `/api/gateway/connect-remote-host` |

**Response Example**

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| errorCode | Int32 | (Required) Error code. `0` = success. |
| errorMsg | String | (Required) Error message |

---

#### 2.10.2.24 disconnect-remote-host — Disconnect from Remote Server

No request parameters.

| Method | URL |
|--------|-----|
| GET | `/api/gateway/disconnect-remote-host` |

**Response Example**

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| errorCode | Int32 | (Required) Error code. `0` = success. |
| errorMsg | String | (Required) Error message |

---

#### 2.10.2.25 file-server-items — Get Gateway File Server Listing

Retrieves the list of files and subdirectories in the root directory of the gateway file server. Subdirectories correspond to machines and are named after the machine's IP address. No request parameters.

| Method | URL |
|--------|-----|
| GET | `/api/gateway/file-server-items` |

**Response Example**

```json
{
  "totalSpace": "10.00 GB",
  "usedSpace": "120 B",
  "files": [],
  "subDirs": [
    {
      "name": "127.0.0.1",
      "size": "20 B",
      "machineName": "1",
      "machineID": "1",
      "ip": "127.0.0.1",
      "status": "Activated"
    },
    {
      "name": "127.0.0.2",
      "size": "100 B",
      "machineName": "2",
      "machineID": "2",
      "ip": "127.0.0.2",
      "status": "Activated"
    }
  ]
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| totalSpace | String | (Required) Total storage space |
| usedSpace | String | (Required) Used storage space |
| files | Object[] | (Required) List of files in the root directory. Typically empty. |
| subDirs | Object[] | (Required) List of subdirectories in the root directory. |
| name | String | File or directory name |
| size | String | File or directory size |
| machineName | String | Associated machine name |
| machineID | String | Associated machine identifier |
| ip | String | Associated machine IP address |
| status | String | Status. Values: `Unlinked` (not associated), `Activated` (activated), `Unactivated` (not activated). |

---

#### 2.10.2.26 delete-file-server-item — Delete Gateway File Server Item

Deletes a specified file or subdirectory from the root directory.

| Method | URL |
|--------|-----|
| GET | `/api/gateway/delete-file-server-item` |

| Request Parameter | Type | Description |
|-------------------|------|-------------|
| fileName | String | File name |
| dirName | String | Subdirectory name |

**Response Example**

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

| Response Parameter | Type | Description |
|--------------------|------|-------------|
| errorCode | Int32 | (Required) Error code. `0` = success. |
| errorMsg | String | (Required) Error message |
