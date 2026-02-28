## 2.10 网关功能接口

网关功能接口用于命令网关执行特定功能，包括 Gateway 服务功能接口和 Core 服务功能接口。

---

### 2.10.1 Core 服务功能接口

基地址 `/api/core`。

---

#### 2.10.1.1 info 获取 Core 服务信息

此接口无请求参数。

| 方法 | URL |
|------|-----|
| GET | `/api/core/info` |

返回示例

```json
{
  "version": "1.19.4.18"
}
```

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| version | String | (必需)版本号 |

---

#### 2.10.1.2 license-info 获取许可信息

获取许可详情，包括截至日期、许可数量、许可类型、许可状态等。此接口无请求参数。

| 方法 | URL |
|------|-----|
| GET | `/api/core/license-info` |

返回示例

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

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| isValid | Bool | (必需)许可是否有效，true=有效，false=无效。 |
| license | object | (必需)许可详情 |
| company | String | 授权该许可的公司名称。中性化版本不返回。 |
| product | String | (必需)许可对应的产品名称。 |
| machineCount | Int32 | (必需)许可总数。 |
| plcCount | Int32 | PLC 许可数。如不返回，PLC 许可数为许可总数。 |
| robotCount | Int32 | 机器人许可数。如不返回，机器人许可数为许可总数。 |
| cncCount | Int32 | CNC 许可数。如不返回，CNC 许可数为许可总数。 |
| laserCount | Int32 | 激光切割机许可数。如不返回，激光切割机许可数为许可总数。 |
| featureAppDNC | Bool | (必需)是否启用文件传输专业版，true=启用，false=不启用。 |
| licType | String | (必需)许可类型，Basic=基础版，Standard=标准版，Full=扩展版，DNC=DNC 版，PLC=PLC 版。 |
| uid | String | (必需)许可对应的网关 UID。 |
| expiration | String | (必需)截止有效期。 |

---

#### 2.10.1.3 service-status 获取服务状态

此接口获取包括云平台、MODBUS、MQTT、数据库、本地缓存等服务状态，此接口无请求参数。

| 方法 | URL |
|------|-----|
| GET | `/api/core/service-status` |

返回示例

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

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| mqtt | Int32 | (必需)MQTT 状态，参考表格 49 网关服务运行状态。 |
| queueSizeMqtt | Int32 | (必需)MQTT 队列长度 |
| modbus | Int32 | (必需)MODBUS 状态，参考表格 49 网关服务运行状态。 |
| queueSizeModbus | Int32 | (必需)MODBUS 队列长度 |
| tbCloud | Int32 | (必需)云平台状态，参考表格 49 网关服务运行状态。 |
| queueSizeTbCloud | Int32 | (必需)云平台队列长度 |
| localDB | Int32 | (必需)本地缓存状态，参考表格 49 网关服务运行状态。 |
| queueSizeLocalDB | Int32 | (必需)本地缓存队列长度 |
| writeData | Int32 | (必需)数据库状态，参考表格 49 网关服务运行状态。 |
| queueSizeWriteData | Int32 | (必需)数据库队列长度 |
| localDBLog | Int32 | (必需)日志数据库状态，参考表格 49 网关服务运行状态。 |
| queueSizeLocalDBLog | Int32 | (必需)日志数据库队列长度 |
| hubBroadcasting | Int32 | (必需)hub 广播状态，参考表格 49 网关服务运行状态。 |
| queueSizeHubBroadcasting | Int32 | (必需)hub 广播队列长度 |
| tbHubBroadcasting | Int32 | (必需)云平台广播状态，参考表格 49 网关服务运行状态。 |
| queueSizeTbHubBroadcasting | Int32 | (必需)云平台广播队列长度 |
| needRestart | Bool | (必需)需要重启服务以应用设置 |

---

#### 2.10.1.4 upload-license 上传网关许可

以字符串流的形式上传网关许可。

| 方法 | URL |
|------|-----|
| POST | `/api/core/upload-license` |

请求体示例

```json
{
  "Base64": "string"
}
```

> 网关许可文件应在上传前转换为 Base64 字符串格式，网关将使用 Base64 字符串格式读取文件内容。

返回示例

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| errorCode | Int32 | (必需)错误码，0 代表成功。 |
| errorMsg | String | (必需)错误内容 |

---

### 2.10.2 Gateway 服务功能接口

基地址 `/api/gateway`。

---

#### 2.10.2.1 alias 获取网关名称

此接口无请求参数。

| 方法 | URL |
|------|-----|
| GET | `/api/gateway/alias` |

返回示例

```json
{
  "alias": "iotgw"
}
```

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| alias | String | (必需)网关名称 |

---

#### 2.10.2.2 update-alias 修改网关名称

此接口无请求参数。注：此修改在硬件重启后生效。

| 方法 | URL |
|------|-----|
| POST | `/api/gateway/update-alias` |

请求体示例 application/json

```json
{
  "alias": "newAlias"
}
```

| 请求参数 | 类型 | 说明 |
|----------|------|------|
| alias | String | (必需)网关名称 |

返回示例

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| errorCode | Int32 | (必需)错误码，0 代表成功。 |
| errorMsg | String | (必需)错误内容 |

---

#### 2.10.2.3 reboot 网关硬件重启

等效于在网关管理页面点击"电源"-"重启"按钮。此接口无请求参数。

| 方法 | URL |
|------|-----|
| GET | `/api/gateway/reboot` |

返回示例

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| errorCode | Int32 | (必需)错误码，0 代表成功。 |
| errorMsg | String | (必需)错误内容 |

---

#### 2.10.2.4 restart 重启所有服务

此接口用于重启 Core 服务与 Gateway 服务。此重启服务与网关主页的"重启服务"按键功能不同。此接口无请求参数。

| 方法 | URL |
|------|-----|
| GET | `/api/gateway/restart` |

返回示例

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| errorCode | Int32 | (必需)错误码，0 代表成功。 |
| errorMsg | String | (必需)错误内容 |

---

#### 2.10.2.5 restart-service 重启 Core 服务

用户修改了机台配置、机组配置、任务配置，或通讯配置后，需要使用此接口，等效于在网关主页点击"重启服务"按钮。此接口无请求参数。

| 方法 | URL |
|------|-----|
| GET | `/api/gateway/restart-service` |

返回示例

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| errorCode | Int32 | (必需)错误码，0 代表成功。 |
| errorMsg | String | (必需)错误内容 |

---

#### 2.10.2.6 shut-down 网关关机

等效于在网关管理页面点击"电源"-"关机"按钮。此接口无请求参数。

| 方法 | URL |
|------|-----|
| GET | `/api/gateway/shut-down` |

返回示例

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| errorCode | Int32 | (必需)错误码，0 代表成功。 |
| errorMsg | String | (必需)错误内容 |

---

#### 2.10.2.7 internet-connection 获取网关网络状态

获取网关与因特网的连接状态。此接口无请求参数。

| 方法 | URL |
|------|-----|
| GET | `/api/gateway/internet-connection` |

返回示例

```json
{
  "isOnline": true
}
```

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| isOnline | Bool | (必需)是否联网，true=已连接，false=未连接。 |

---

#### 2.10.2.8 time 获取网关当前时间

此接口无请求参数。

| 方法 | URL |
|------|-----|
| GET | `/api/gateway/time` |

返回示例

```json
{
  "localTime": "2025-06-30T13:51:19.2864043+08:00"
}
```

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| localTime | String | (必需)网关本地时间（ISO 8601） |

---

#### 2.10.2.9 sync-time 同步网关时间

| 方法 | URL |
|------|-----|
| GET | `/api/gateway/sync-time` |

| 请求参数 | 类型 | 说明 |
|----------|------|------|
| timeServerAddress | String | (必需)时间服务器地址，多个地址用";"分隔，网关会首先尝试与第一个地址同步时间，如失败，则尝试下一个地址，示例：`ntp1.aliyun.com;ntp2.aliyun.com;ntp3.aliyun.com;ntp4.aliyun.com;` |

返回示例

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| errorCode | Int32 | (必需)错误码，0 代表成功。 |
| errorMsg | String | (必需)错误内容 |

---

#### 2.10.2.10 time-zone 获取网关时区

此接口无请求参数。

| 方法 | URL |
|------|-----|
| GET | `/api/gateway/time-zone` |

返回示例

```json
{
  "timeZoneID": "China Standard Time"
}
```

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| timeZoneID | String | (必需)网关时区（Microsoft Windows 时区 ID） |

---

#### 2.10.2.11 time-zones 获取时区选项

此接口无请求参数。

| 方法 | URL |
|------|-----|
| GET | `/api/gateway/time-zones` |

返回示例

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

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| timeZoneIDs | String[] | (必需)网关时区选项（Microsoft Windows 时区 ID） |

---

#### 2.10.2.12 update-time-zone 修改网关时区

| 方法 | URL |
|------|-----|
| POST | `/api/gateway/update-time-zone` |

请求体示例 application/json

```json
{
  "timeZoneID": "China Standard Time"
}
```

| 请求参数 | 类型 | 说明 |
|----------|------|------|
| timeZoneID | String | (必需)网关时区（Microsoft Windows 时区 ID），可通过 2.10.2.11 time-zones 获取时区选项获得可用选项。 |

返回示例

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| errorCode | Int32 | (必需)错误码，0 代表成功。 |
| errorMsg | String | (必需)错误内容 |

---

#### 2.10.2.13 network-adapters 获取网关网络适配器列表

此接口无请求参数。

| 方法 | URL |
|------|-----|
| GET | `/api/gateway/network-adapters` |

返回示例

```json
{
  "names": [
    "LAN1",
    "LAN2",
    "WLAN"
  ]
}
```

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| names | String[] | (必需)网络适配器名称 |

---

#### 2.10.2.14 lan 获取有线网设置

| 方法 | URL |
|------|-----|
| GET | `/api/gateway/lan` |

| 请求参数 | 类型 | 说明 |
|----------|------|------|
| name | String | (必需)网络适配器名称，范围：LAN1 或 LAN2。 |

返回示例

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

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| name | String | (必需)网络适配器名称，范围：LAN1 或 LAN2。 |
| macAddress | String | (必需)物理地址 |
| state | String | (必需)状态，范围：Connected，Disconnected。 |
| isDHCPEnabled | Bool | (必需)自动获得 IP 地址 |
| ipAddress | String | (必需)IP 地址 |
| subMask | String | (必需)子网掩码 |
| defaultGateway | String | (必需)默认网关 |
| isDNSServerDHCPEnabled | Bool | (必需)自动获得 DNS 服务器地址 |
| dnsServer1 | String | (必需)首选 DNS 服务器 |
| dnsServer2 | String | (必需)备用 DNS 服务器 |

---

#### 2.10.2.15 update-lan 修改网关有线网设置

| 方法 | URL |
|------|-----|
| POST | `/api/gateway/update-lan` |

请求体示例 application/json

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

| 请求参数 | 类型 | 说明 |
|----------|------|------|
| name | String | (必需)目标网络适配器名称，范围：LAN1 或 LAN2。 |
| isDHCPEnabled | Bool | 自动获得 IP 地址 |
| ipAddress | String | IP 地址 |
| subMask | String | 子网掩码 |
| defaultGateway | String | 默认网关 |
| isDNSServerDHCPEnabled | Bool | 自动获得 DNS 服务器地址 |
| dnsServer1 | String | 首选 DNS 服务器 |
| dnsServer2 | String | 备用 DNS 服务器 |

返回示例

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

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| name | String | (必需)网络适配器名称，范围：LAN1 或 LAN2。 |
| macAddress | String | (必需)物理地址 |
| state | String | (必需)状态，范围：Connected，Disconnected。 |
| isDHCPEnabled | Bool | (必需)自动获得 IP 地址 |
| ipAddress | String | (必需)IP 地址 |
| subMask | String | (必需)子网掩码 |
| defaultGateway | String | (必需)默认网关 |
| isDNSServerDHCPEnabled | Bool | (必需)自动获得 DNS 服务器地址 |
| dnsServer1 | String | (必需)首选 DNS 服务器 |
| dnsServer2 | String | (必需)备用 DNS 服务器 |

---

#### 2.10.2.16 wifi 获取无线网设置

此接口无请求参数。

| 方法 | URL |
|------|-----|
| GET | `/api/gateway/wifi` |

返回示例

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

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| wifiName | String | (必需)无线网络 SSID |
| signalStrength | Int32 | 范围：0-100，越大信号越强 |
| macAddress | String | (必需)物理地址 |
| state | String | (必需)状态，范围：Connected，Disconnected。 |
| isDHCPEnabled | Bool | (必需)自动获得 IP 地址 |
| ipAddress | String | (必需)IP 地址 |
| subMask | String | (必需)子网掩码 |
| defaultGateway | String | (必需)默认网关 |
| isDNSServerDHCPEnabled | Bool | (必需)自动获得 DNS 服务器地址 |
| dnsServer1 | String | (必需)首选 DNS 服务器 |
| dnsServer2 | String | (必需)备用 DNS 服务器 |

---

#### 2.10.2.17 update-wifi 修改无线网设置

| 方法 | URL |
|------|-----|
| POST | `/api/gateway/update-wifi` |

请求体示例 application/json

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

| 请求参数 | 类型 | 说明 |
|----------|------|------|
| isDHCPEnabled | Bool | 自动获得 IP 地址 |
| ipAddress | String | IP 地址 |
| subMask | String | 子网掩码 |
| defaultGateway | String | 默认网关 |
| isDNSServerDHCPEnabled | Bool | 自动获得 DNS 服务器地址 |
| dnsServer1 | String | 首选 DNS 服务器 |
| dnsServer2 | String | 备用 DNS 服务器 |

返回示例

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

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| wifiName | String | (必需)无线网络 SSID |
| signalStrength | Int32 | 范围：0-100，越大信号越强 |
| macAddress | String | (必需)物理地址 |
| state | String | (必需)状态，范围：Connected，Disconnected。 |
| isDHCPEnabled | Bool | (必需)自动获得 IP 地址 |
| ipAddress | String | (必需)IP 地址 |
| subMask | String | (必需)子网掩码 |
| defaultGateway | String | (必需)默认网关 |
| isDNSServerDHCPEnabled | Bool | (必需)自动获得 DNS 服务器地址 |
| dnsServer1 | String | (必需)首选 DNS 服务器 |
| dnsServer2 | String | (必需)备用 DNS 服务器 |

---

#### 2.10.2.18 search-wifi 搜索无线网

此接口无请求参数。

| 方法 | URL |
|------|-----|
| GET | `/api/gateway/search-wifi` |

返回示例

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

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| wifiName | String | (必需)无线网络 SSID |
| signalStrength | Int32 | 范围：0-100，越大信号越强 |
| state | String | (必需)状态，范围：Connected，Disconnected。 |

---

#### 2.10.2.19 connect-wifi 连接无线网

| 方法 | URL |
|------|-----|
| GET | `/api/gateway/connect-wifi` |

| 请求参数 | 类型 | 说明 |
|----------|------|------|
| wifiName | String | (必需)目标无线网络 SSID |
| wifiPassword | String | 目标无线网络密码 |

返回示例

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| errorCode | Int32 | (必需)错误码，0 代表成功。 |
| errorMsg | String | (必需)错误内容 |

---

#### 2.10.2.20 disconnect-wifi 断开无线网

此接口无请求参数。

| 方法 | URL |
|------|-----|
| GET | `/api/gateway/disconnect-wifi` |

返回示例

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| errorCode | Int32 | (必需)错误码，0 代表成功。 |
| errorMsg | String | (必需)错误内容 |

---

#### 2.10.2.21 static-routing 获取静态路由设置

此接口无请求参数。

| 方法 | URL |
|------|-----|
| GET | `/api/gateway/static-routing` |

返回示例

```json
{
  "staticRouting": "0.0.0.0, 0.0.0.0, 192.168.100.1;"
}
```

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| staticRouting | Int32 | (必需)静态路由。 |

---

#### 2.10.2.22 update-static-routing 修改静态路由设置

| 方法 | URL |
|------|-----|
| POST | `/api/gateway/update-static-routing` |

请求体示例 application/json

```json
{
  "staticRouting": "0.0.0.0, 0.0.0.0, 192.168.100.1;"
}
```

| 请求参数 | 类型 | 说明 |
|----------|------|------|
| staticRouting | Int32 | (必需)静态路由。 |

返回示例

```json
{
  "staticRouting": "0.0.0.0, 0.0.0.0, 192.168.100.1;"
}
```

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| staticRouting | Int32 | (必需)静态路由。 |

---

#### 2.10.2.23 connect-remote-host 连接远程服务器

此接口无请求参数。

| 方法 | URL |
|------|-----|
| GET | `/api/gateway/connect-remote-host` |

返回示例

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| errorCode | Int32 | (必需)错误码，0 代表成功。 |
| errorMsg | String | (必需)错误内容 |

---

#### 2.10.2.24 disconnect-remote-host 断开远程服务器

此接口无请求参数。

| 方法 | URL |
|------|-----|
| GET | `/api/gateway/disconnect-remote-host` |

返回示例

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| errorCode | Int32 | (必需)错误码，0 代表成功。 |
| errorMsg | String | (必需)错误内容 |

---

#### 2.10.2.25 file-server-items 获取网关文件服务器列表

获取网关文件服务器根目录下的文件与子目录列表。子目录与机台对应，名称为机台的 IP 地址。此接口无请求参数。

| 方法 | URL |
|------|-----|
| GET | `/api/gateway/file-server-items` |

返回示例

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

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| totalSpace | String | (必需)总空间 |
| usedSpace | String | (必需)已使用空间 |
| files | Object[] | (必需)根目录下的文件列表，一般为空。 |
| subDirs | Object[] | (必需)根目录下的子目录列表。 |
| name | String | 文件/目录名 |
| size | String | 文件/目录大小 |
| machineName | String | 关联机台名称 |
| machineID | String | 关联机台标识 |
| ip | String | 关联机台 ip |
| status | String | 状态，范围 Unlinked（未关联），Activated（已激活），Unactivated（未激活）。 |

---

#### 2.10.2.26 delete-file-server-item 删除网关文件服务器项目

删除根目录下指定文件或子目录。

| 方法 | URL |
|------|-----|
| GET | `/api/gateway/delete-file-server-item` |

| 请求参数 | 类型 | 说明 |
|----------|------|------|
| fileName | String | 文件名 |
| dirName | String | 子目录名 |

返回示例

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

| 返回参数 | 类型 | 说明 |
|----------|------|------|
| errorCode | Int32 | (必需)错误码，0 代表成功。 |
| errorMsg | String | (必需)错误内容 |
