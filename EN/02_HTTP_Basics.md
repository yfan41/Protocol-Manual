# II. HTTP Communication

## 2.1 Basic Description

The service runs on the standard HTTP port (port 80). Unless otherwise noted, the base URL for all requests is `/api/cnc`.

A standard request URL format is as follows:

```
http://{GatewayIP}/api/cnc/{interface}?MachineID={target machineID}
```

For example, if the gateway IP address is 192.168.100.1, and you want to retrieve the running status of the main channel of a machine with IP 192.168.1.10 and MachineID 1010 (see section 1.1.1 machineID — Machine Identifier for an explanation of machineID), use the `/readCNCStatus` interface. The corresponding request URL is:

```
http://192.168.100.1/api/cnc/readCNCStatus?MachineID=1010
```

- All interfaces only accept HTTP data with `Content-Type: application/json`.
- Except for some file-related interfaces, most interfaces return `application/json` type HTTP data.
- All data encoding must be UTF-8.
- Some interfaces described below require additional input parameters appended to the request URL.

---

## 2.2 Error Handling

When any request encounters an error, an error object is returned as follows:

```json
{
  "errorCode": -1,
  "errorMsg": "Unknown error"
}
```

| Return Parameter | Type | Description |
|-----------------|------|-------------|
| errorCode | Int32 | (Required) Error code; 0 indicates success |
| errorMsg | String | (Required) Error message |

When the operation succeeds, the corresponding data is returned with HTTP status code 200.

When an error occurs, an error code is returned with an HTTP status code of typically 400, 409, 500, etc. Common error codes are as follows:

**Table 51 Common Error Codes**

| Error Code | Content | Description |
|-----------|---------|-------------|
| 0 | Success | Operation executed successfully |
| 3 | Machine off or offline | Cannot communicate with the machine; machine is powered off or there is a hardware network failure (machine NIC, switch, cable, etc.) |
| 7 | No permission | Interface is protected or outside the authorization scope, or the authentication credentials are invalid |
| 102 | Machine network fault, unable to retrieve data | Communication with the machine is possible but data cannot be retrieved; the machine's network settings have an issue |
| 125 | Invalid file path | The specified file path is invalid |
| 142 | File already exists | A file with the same name already exists in the target directory |
| 143 | File in use | The file to be operated on is currently in use |
| 144 | File write-protected | The file to be operated on is write-protected |
| 158 | File not found | The target file does not exist in the target directory |
| 10003 | machineID does not exist | Incorrect machineID; the machine is not activated, or the gateway service was not restarted after editing the machine IP or activation status |
| 10006 | Interface not authorized | Contact Bivrost to obtain authorization for this interface |

---

## 2.3 Authentication Methods

When security control is enabled in the gateway settings, users must provide authentication credentials to use authorized APIs. If security control is disabled, authentication can be skipped and all interfaces can be used directly.

The gateway supports two authentication methods: JWT method and Secret Key method.

### 2.3.1 JWT Method

The user calls the authentication interface `/api/auth/login` to log in with a username and password, and receives a JWT token for the corresponding user. The token is valid for 24 hours; after expiration, the user must log in again to obtain a new token. Include `Authorization: Bearer <token>` in the request header to use the authorized APIs under that user's permission level. If the logged-in user has administrator privileges, all interfaces except protected APIs can be used.

### 2.3.2 Secret Key Method

Generate a secret key on the gateway Settings → Manage Users → User Security Settings page. The key is permanently valid. Include `Authorization: Bearer <secret key>` in the request header to use the authorized APIs under that user's permission level. If the logged-in user has administrator privileges, all interfaces except protected APIs can be used.

---

## 2.4 Authentication Interface

Authentication interface base URL: `/api/auth/`.

### 2.4.1 login

```
POST  /api/auth/login
```

Request body example (application/json):

```json
{
  "username": "username",
  "password": "password"
}
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| username | String | (Required) Username |
| password | String | (Required) Password |

**Response Example**

```json
{
  "token": "a2JhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9eyJ1bmlxdWV.bmFtZSI6ImFkbWluIiwibmJmIjoxNjIwMTYzOTUwLCJleHAiOjE2MjAxNzExNTAsImlhdCI6MTYyMDE2Mzk1MH0..."
}
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| token | String | (Required) User token. Valid for 24 hours; log in again after expiration to obtain a new token |

### 2.4.2 change-password

This interface is only available when using the JWT authentication method.

```
POST  /api/auth/change-password
```

Request body example (application/json):

```json
{
  "currentPassword": "currentPassword",
  "newPassword": "newPassword"
}
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| currentPassword | String | (Required) Current password |
| newPassword | String | (Required) New password |

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
| errorCode | Int32 | (Required) Error code; 0 indicates success |
| errorMsg | String | (Required) Error message |
