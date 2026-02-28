# 二、HTTP 通讯

## 2.1 基本说明

服务运行于 HTTP 标准端口（80 端口），如无特殊说明，请求的基地址均为 `/api/cnc`。

一个标准的请求地址格式如下：

```
http://{网关IP}/api/cnc/{接口名}?MachineID={目标机台machineID}
```

例如网关的 IP 地址为 192.168.100.1，要获取该网关连接的 IP 为 192.168.1.10、MachineID 为 1010（machineID 解释见 1.1.1 machineID 机台标识）的机台主通道的运行状态，应使用 `/readCNCStatus` 接口，对应请求地址为：

```
http://192.168.100.1/api/cnc/readCNCStatus?MachineID=1010
```

- 所有接口只接收 Content-Type 为 `application/json` 类型的 HTTP 数据。
- 除部分文件相关的接口外，大部分接口返回 `application/json` 类型的 HTTP 数据。
- 所有数据的编码必须是 UTF-8。
- 以下说明中，部分接口需要在请求地址中额外补充其它输入量。

---

## 2.2 错误处理

任何请求发生错误时，将返回错误对象如下：

```json
{
  "errorCode": -1,
  "errorMsg": "未知错误"
}
```

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| errorCode | Int32 | （必需）错误代码，0 代表成功 |
| errorMsg | String | （必需）错误内容 |

当操作成功时，返回相应数据，此时 HTTP 状态码为 200。

出现错误时，返回错误代码，此时 HTTP 状态码一般是 400、409、500 等，常见返回错误代码如下：

**表格 51 常见错误代码**

| 错误代码 | 内容 | 说明 |
|---------|------|------|
| 0 | 成功 | 操作执行成功 |
| 3 | 机台关闭或离线 | 无法与机床进行通讯，机床关机或网络硬件（机床网口、交换机、网线等）故障 |
| 7 | 没有权限 | 接口受保护或不在授权范围内，或鉴权信息无效 |
| 102 | 机台网络故障，无法获取数据 | 能与机床进行通讯，但无法获取数据，机床网络设置有问题 |
| 125 | 文件路径无效 | 输入的文件路径无效 |
| 142 | 文件已存在 | 目标目录中已存在同名文件 |
| 143 | 文件正在使用中 | 要操作的文件正在被使用 |
| 144 | 文件写保护 | 要操作的文件处于写保护状态 |
| 158 | 文件不存在 | 目标目录中无目标文件 |
| 10003 | machineID 不存在 | machineID 有误，对应机台未激活，编辑机台 IP、激活状态后是否未重启网关服务 |
| 10006 | 接口未授权 | 需使用未授权接口请联系彼络 |

---

## 2.3 鉴权方式

在网关设置中，启用安全控制后，用户必须提供鉴权信息才能使用授权 API。如关闭安全控制，则可跳过鉴权，直接使用所有接口。

网关支持以下两种鉴权方式：JWT 方式与密钥方式。

### 2.3.1 JWT 方式

用户使用鉴权接口 `/api/auth/login`，以用户名密码登录后，获取对应用户的 JWT token。Token 的有效时间为 24 小时，过期后需再次登录获取新 token。请求时在 Header 里带 `Authorization: Bearer <token>`，可使用该用户权限下的授权 API。如登录用户有管理员权限，可使用除受保护 API 以外的所有接口。

### 2.3.2 密钥方式

在网关的设置-管理用户-用户安全设置界面，生成密钥，密钥永久有效。请求时在 Header 里带 `Authorization: Bearer <密钥>`，可使用该用户权限下的授权 API。如登录用户有管理员权限，可使用除受保护 API 以外的所有接口。

---

## 2.4 鉴权接口

鉴权接口基地址 `/api/auth/`。

### 2.4.1 login 登录

```
POST  /api/auth/login
```

请求体示例 application/json：

```json
{
  "username": "username",
  "password": "password"
}
```

**请求参数**

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| username | String | （必需）用户名 |
| password | String | （必需）密码 |

**返回示例**

```json
{
  "token": "a2JhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9eyJ1bmlxdWV.bmFtZSI6ImFkbWluIiwibmJmIjoxNjIwMTYzOTUwLCJleHAiOjE2MjAxNzExNTAsImlhdCI6MTYyMDE2Mzk1MH0..."
}
```

**返回参数**

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| token | String | （必需）用户令牌。有效时间为 24 小时，过期后需再次登录获取新令牌 |

### 2.4.2 change-password 修改密码

此接口仅限 JWT 鉴权方式下使用。

```
POST  /api/auth/change-password
```

请求体示例 application/json：

```json
{
  "currentPassword": "currentPassword",
  "newPassword": "newPassword"
}
```

**请求参数**

| 请求参数 | 类型 | 说明 |
|---------|------|------|
| currentPassword | String | （必需）当前密码 |
| newPassword | String | （必需）新密码 |

**返回示例**

```json
{
  "errorCode": 0,
  "errorMsg": "Success"
}
```

**返回参数**

| 返回参数 | 类型 | 说明 |
|---------|------|------|
| errorCode | Int32 | （必需）错误代码，0 代表成功 |
| errorMsg | String | （必需）错误内容 |
