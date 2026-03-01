## 2.6 File Management Interfaces

The file management interfaces connect to a target file server and provide functions to read file lists, receive, send, delete, lock, and unlock files, and create or delete directories.

Currently supported file server types are: **Machine Storage**, **FTP Server**, **Shared Folder**, etc. (see *User Manual*, section 5.3.1 Add Machine — Program Transfer Settings for each machine type). The default is **Machine Storage**. Some CNC systems support enabling a local FTP server or shared folder option for file transfer. For machines with limited onboard storage, or machines that do not support direct access to their storage, file transfer can be achieved through an external file server.

Most file management interfaces accept the optional request parameter `dirAtCNC` or `subDir` to specify the directory path on the machine or file server. If neither `dirAtCNC` nor `subDir` is provided, the corresponding file operation is performed in the default root directory. The root directory path can be configured by the user (see *User Manual*, section 5.3.1 Add Machine). If the user has not configured a root directory path, the default root directory paths shown below are used.

**Table 55 — Default Root Directory Paths by CNC System**

| File Server Type | System (Model) | Custom Path Supported | Default Root Directory Path |
|-----------------|----------------|-----------------------|-----------------------------|
| Machine Storage | Brother [TC Series] | No | / |
| Machine Storage | Brother [S Series] | Yes | / |
| Machine Storage | Delta [Universal] | Yes | B:\ |
| Machine Storage | Fagor [All Models] | Yes | MEMORY |
| Machine Storage | Fanuc [0i-D, 0i-F, 30i, 31i, 32i, 35i, Power Motion i-A] ★ | Yes | //CNC_MEM/ |
| Machine Storage | Fanuc [Models other than 0i-D, 0i-F, 30i, 31i, 32i, 35i, Power Motion i-A] | No | Foreground directory configured on the machine (not fixed; e.g. //CNC_MEM/) |
| Machine Storage | GSK [980, 988] | Yes | /user/NCPROG |
| Machine Storage | Haas [Universal, MT-CONNECT] | Yes | (none) |
| Machine Storage | Heidenhain [TNC640, TNC640 DNC] | Yes | TNC:\nc_prog |
| Machine Storage | Heidenhain [iTNC530] | Yes | TNC:\ |
| Machine Storage | Lynuc [All Models] | Yes | /home/Lynuc/Users/NCFiles |
| Machine Storage | Mitsubishi [All Models] ★★ | Yes | PRG\USER\ |
| Machine Storage | Mock (Simulated Machine) | Yes | / |
| Machine Storage | Okuma [All Models] | Yes | MD1 |
| Machine Storage | Rexroth [OPC UA] | Yes | Filesystem/prog |
| Machine Storage | Siemens [Universal] | Yes | /nckfs |
| Machine Storage | Siemens [OPC UA] | Yes | Sinumerik/FileSystem/PartProgram |
| Machine Storage | Siemens [810D/840D] | Yes | mpf.dir |
| Machine Storage | Other CNC models supporting program transfer | No | Machine default storage directory |
| FTP Server | | Yes | FTP server root directory |
| Shared Folder | | Yes | (none) |
| Shared Folder (Win XP) | | Yes | (none) |
| Wireless Transfer Box | | Yes | / |
| Gateway File Server | | Yes | / |

Yes: Supported; No: Not supported

★ Fanuc [0i-F, 30i, 31i, 32i, 35i, Power Motion i-A]: The target directory path can be changed to an external CF card, e.g., `//MEMCARD/`.

★★ Mitsubishi [M700 Series, M800 Series]: The target directory path can be changed to an external CF card (M700 Series) or SD card (M800 Series), e.g., `IC1`.

---

## 2.6.1 readProgramList — Read Machine File List

```
GET  /api/cnc/readProgramList?machineID=MACHINEID&dirAtCNC=DIRATCNC&subDir=SUBDIR
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| dirAtCNC | String | Target directory path on the machine. If not specified, defaults to the default root directory path (see Table 55). |
| subDir | String | Target subdirectory path. If `dirAtCNC` is not provided, the root directory path and subdirectory path are automatically combined into `dirAtCNC`. If both `dirAtCNC` and `subDir` are provided, `subDir` is ignored. |

**Response Example**

```json
{
  "dirAtCNC": "Dir/Prog",
  "programs": [
    "1111",
    "2322",
    "3222"
  ],
  "subDirs": [
    "dir1",
    "dir2"
  ]
}
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| dirAtCNC | String | (Required) The resolved directory path. Generally matches the `dirAtCNC` request parameter, except in the following cases: (1) The device only has a single unnamed default directory — in this case, the returned `dirAtCNC` is empty; (2) If `dirAtCNC` was not specified or was empty in the request, the returned value is the default root directory path; (3) If the specified path exists but does not conform to the system path format, the returned value is the auto-corrected path. |
| programs | String[] | (Required) List of programs (files) in the specified directory |
| subDirs | String[] | (Required) List of subdirectories in the specified directory. Some CNC systems cannot retrieve folder lists; in such cases, `subDirs` is empty. |

**Table 56 — Subdirectory Listing Support by CNC System**

| File Server Type | System (Model) | Get File List | Get Subdirectory List |
|-----------------|----------------|---------------|-----------------------|
| Machine Storage | Brother [All Models] | Yes | Yes |
| Machine Storage | Fagor [All Models] | Yes | No |
| Machine Storage | Fanuc [0i-D, 0i-F, 30i, 31i, 32i, 35i, Power Motion i-A] | * | Yes |
| Machine Storage | Fanuc [Other Models] | Yes | No |
| Machine Storage | Heidenhain [All Models] | Yes | Yes |
| Machine Storage | Lynuc [All Models] | Yes | Yes |
| Machine Storage | Mitsubishi [All Models] | Yes | No |
| Machine Storage | Mock (Simulated Machine) | Yes | Yes |
| Machine Storage | Okuma [All Models] | Yes | Yes |
| Machine Storage | Rexroth [OPC UA] | Yes | Yes |
| Machine Storage | Siemens [All Models] | Yes | Yes |
| Machine Storage | Other models supporting program transfer | Yes | No |
| FTP Server | | Yes | Yes |
| Shared Folder | | Yes | Yes |
| Shared Folder (Win XP) | | Yes | Yes |
| Wireless Transfer Box | | Yes | Yes |
| Gateway File Server | | Yes | Yes |

Yes: Supported; No: Not supported

\*: Fanuc [0i-D, 0i-F, 30i, 31i, 32i, 35i, Power Motion i-A] returns standard Fanuc program names (letter O + digits), automatically removing leading zeros from the numeric portion. For example, if the program is named `O0010` on the machine, `010` is returned. Non-standard named programs are returned as-is (e.g., `SAMPLE`).

---

## 2.6.2 receiveFileStream — Receive Machine File (Stream Method)

This interface can receive both text and binary files. Compared to section 2.6.3 `receiveFile`, this interface additionally supports files larger than 10 MB. It is recommended to use `/receiveFileStream`.

```
GET  /api/cnc/receiveFileStream?machineID=MACHINEID&fileName=FILENAME&dirAtCNC=DIRATCNC&subDir=SUBDIR
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| fileName | String | (Required) Target file name. Refer to file names returned by section 2.6.1 `readProgramList` or section 2.6.15 `readAllPrograms`. Some CNC systems (e.g., GSK, Siemens) require that the file name include the file extension. |
| dirAtCNC | String | Target directory path. If not specified, defaults to the default root directory path (see Table 55). |
| subDir | String | Target subdirectory path. If `dirAtCNC` is not provided, the root and subdirectory paths are combined. If both are provided, `subDir` is ignored. |

**Response Example**

For text files, response body example (`application/octet-stream`):

```gcode
%
O0001
G21 G90 G40
T01 M06
G90 G0 G54 X12.64 Y88.0 s2546
G43 H01 Z15.0 M8
G81 G98 Z-20. R1.F200.
x70.
X85.
x100.
x170.
X185.
x200.
G80
G00 x100. Y200
G28 G91 z0 M9
M30
%
```

For binary files, the response body contains the raw binary content.

---

## 2.6.3 receiveFile — Receive Machine File

This interface can receive both text and binary files. Compared to section 2.6.2 `receiveFileStream`, this interface does not support files larger than 10 MB. It is recommended to use `/receiveFileStream`.

```
GET  /api/cnc/receiveFile?machineID=MACHINEID&fileName=FILENAME&dirAtCNC=DIRATCNC&subDir=SUBDIR
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| fileName | String | (Required) Target file name. Refer to file names returned by section 2.6.1 `readProgramList` or section 2.6.15 `readAllPrograms`. Some systems require the file extension. |
| dirAtCNC | String | Target directory path. If not specified, defaults to the default root directory path (see Table 55). |
| subDir | String | Target subdirectory path. Combined with root if `dirAtCNC` is not provided. Ignored if both are provided. |

**Response Example (text file)**

```json
{
  "content": "string",
  "encoding": "us-ascii"
}
```

**Response Example (binary file)**

```json
{
  "base64": "string"
}
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| content | String | File content |
| encoding | String | Original file encoding |
| base64 | String | When the file is binary, the gateway encodes the binary content as a Base64 string. The user can decode it to retrieve the binary file. |

---

## 2.6.4 readCurrentProgram — Receive Currently Running File

```
GET  /api/cnc/readCurrentProgram?machineID=MACHINEID&dirAtCNC=DIRATCNC&subDir=SUBDIR
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1 Basic Description |
| dirAtCNC | String | Target directory path. If not specified, defaults to the default root directory path (see Table 55). |
| subDir | String | Target subdirectory path. Combined with root if `dirAtCNC` is not provided. Ignored if both are provided. |

**Response Example (text file)**

```json
{
  "dirAtCNC": "Dir/Prog",
  "fileName": "09001",
  "content": "string",
  "encoding": "us-ascii"
}
```

**Response Example (binary file)**

```json
{
  "dirAtCNC": "Dir/Prog",
  "fileName": "Program",
  "base64": "string"
}
```

**Response when no program is currently running:**

```json
{
  "errorCode": 158,
  "errorMsg": "Path is not found.",
  "statusMsg": "No content."
}
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| dirAtCNC | String | Directory path of the file currently running on the machine |
| fileName | String | File name of the file currently running on the machine |
| content | String | File content |
| encoding | String | Original file encoding |
| base64 | String | When the file is binary, the binary content is encoded as Base64. The user can decode it to retrieve the binary file. |
| errorCode | Int32 | Error code. `0` = success. |
| errorMsg | String | Error message |
| statusMsg | String | Error details |

---

## 2.6.5 sendFileStream — Send File to Machine (Stream Method)

This interface can send both text and binary files. Compared to section 2.6.6 `sendFile`, this interface additionally supports files larger than 10 MB. It is recommended to use `/sendFileStream`.

Overwriting an existing file with the same name is not supported. Delete the existing file first, then send the new file.

```
POST  /api/cnc/sendFileStream?machineID=MACHINEID&fileName=FILENAME&dirAtCNC=DIRATCNC&subDir=SUBDIR
```

**Request Example**

For text files, request body example (`application/octet-stream`):

```gcode
%
O0001
G21 G90 G40
T01 M06
G90 G0 G54 X12.64 Y88.0 s2546
G43 H01 Z15.0 M8
G81 G98 Z-20. R1.F200.
x70.
X85.
x100.
x170.
X185.
x200.
G80
G00 x100. Y200
G28 G91 z0 M9
M30
%
```

For binary files, upload the raw binary content in the request body.

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| fileName | String | File name on the machine. Some systems (e.g., GSK, Siemens) require the file extension. Refer to file names from `readProgramList` or `readAllPrograms`. For Fanuc systems, `fileName` is not used; the program name is taken from the content between the first and second `\n` characters. |
| dirAtCNC | String | Target directory path. If not specified, defaults to the default root directory path (see Table 55). |
| subDir | String | Target subdirectory path. Combined with root if `dirAtCNC` is not provided. Ignored if both are provided. |

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

## 2.6.6 sendFile — Send File to Machine

This interface can send both text and binary files. Compared to section 2.6.5 `sendFileStream`, this interface does not support files larger than 10 MB. It is recommended to use `/sendFileStream`.

Overwriting an existing file with the same name is not supported. Delete the existing file first, then send the new file.

```
POST  /api/cnc/sendFile?machineID=MACHINEID&fileName=FILENAME&dirAtCNC=DIRATCNC&subDir=SUBDIR
```

**Request Body Example** (application/json)

For text files:

```json
{
  "content": "string",
  "encoding": "us-ascii"
}
```

For binary files:

```json
{
  "base64": "string"
}
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| fileName | String | File name on the machine. Some systems (e.g., GSK, Siemens) require the file extension. For Fanuc systems, `fileName` is not used; the program name is taken from the content between the first and second `\n` characters. |
| dirAtCNC | String | Target directory path. If not specified, defaults to the default root directory path (see Table 55). |
| subDir | String | Target subdirectory path. Combined with root if `dirAtCNC` is not provided. Ignored if both are provided. |
| content | String | File content. Use this parameter for text files. When `content` is defined, the `base64` parameter is ignored. |
| encoding | String | Original file encoding. If not specified, the encoding configured in the machine settings is used. |
| base64 | String | For binary files, encode the binary content as a Base64 string and write it to this parameter. |

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

## 2.6.7 batchSendFile — Batch Send Files to Machines

This interface is the batch version of section 2.6.6 `sendFile`. By specifying multiple target destinations in the request body, the same file can be sent simultaneously to different machines, with different file names and different directory paths.

Overwriting an existing file with the same name is not supported. Delete the existing file first, then send.

```
POST  /api/cnc/batchSendFile
```

**Request Body Example** (application/json)

For text files:

```json
{
  "content": "string",
  "encoding": "us-ascii",
  "targets": [
    { "machineID": "1010", "fileName": "08000" },
    { "machineID": "1011", "fileName": "sample.NC", "DirAtCNC": "dir1" },
    { "machineID": "1012", "fileName": "sample.NC", "SubDir": "Dir/Prog" }
  ]
}
```

For binary files:

```json
{
  "base64": "string",
  "targets": [
    { "machineID": "1011", "fileName": "sample.NC", "SubDir": "Dir/Prog" }
  ]
}
```

Request parameters are the same as section 2.6.6 `sendFile`.

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| targets | Array | (Required) Target destinations for sending the file |
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| fileName | String | File name on the machine. Some systems (e.g., GSK, Siemens) require the file extension. For Fanuc systems, `fileName` is not used; the program name is taken from the content between the first and second `\n` characters. |
| dirAtCNC | String | Target directory path. If not specified, defaults to the default root directory path (see Table 55). |
| subDir | String | Target subdirectory path. Combined with root if `dirAtCNC` is not provided. Ignored if both are provided. |
| content | String | File content. Use for text files. When `content` is defined, `base64` is ignored. |
| encoding | String | Original file encoding. If not specified, the encoding in machine settings is used. |
| base64 | String | For binary files, encode the binary content as Base64 and write it here. |

**Response Example**

```json
[
  {
    "errorCode": 0,
    "errorMsg": "Success"
  },
  {
    "errorCode": 10003,
    "errorMsg": "Machine ID does not exist."
  },
  {
    "errorCode": 142,
    "errorMsg": "Path exists."
  }
]
```

The response body contains the results for each request in the same order as the request body.

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| errorCode | Int32 | (Required) Error code. `0` = success. |
| errorMsg | String | (Required) Error message |

---

## 2.6.8 deleteFile — Delete Machine File

Deletes a specified file from the machine.

```
GET  /api/cnc/deleteFile?machineID=MACHINEID&fileName=FILENAME&dirAtCNC=DIRATCNC&subDir=SUBDIR
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| fileName | String | (Required) Target file name. Refer to file names from `readProgramList` or `readAllPrograms`. Some systems (e.g., GSK, Siemens) require the file extension. |
| dirAtCNC | String | Target directory path. If not specified, defaults to the default root directory path (see Table 55). |
| subDir | String | Target subdirectory path. Combined with root if `dirAtCNC` is not provided. Ignored if both are provided. |

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

## 2.6.9 batchDeleteFile — Batch Delete Machine Files

This interface is the batch version of section 2.6.8 `deleteFile`. By specifying multiple targets in the request body, files on different machines can be deleted in a single call.

```
POST  /api/cnc/batchDeleteFile
```

**Request Body Example** (application/json)

```json
[
  { "machineID": "1010", "fileName": "08000" },
  { "machineID": "1011", "fileName": "sample.NC", "DirAtCNC": "dir1" },
  { "machineID": "1012", "fileName": "sample.NC", "SubDir": "Dir/Prog" }
]
```

Request parameters are the same as section 2.6.8 `deleteFile`.

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| fileName | String | (Required) Target file name. Refer to file names from `readProgramList` or `readAllPrograms`. Some systems require the file extension. |
| dirAtCNC | String | Target directory path. If not specified, defaults to the default root directory path (see Table 55). |
| subDir | String | Target subdirectory path. Combined with root if `dirAtCNC` is not provided. Ignored if both are provided. |

**Response Example**

```json
[
  {
    "errorCode": 0,
    "errorMsg": "Success"
  },
  {
    "errorCode": 10003,
    "errorMsg": "Machine ID does not exist."
  },
  {
    "errorCode": 158,
    "errorMsg": "Path is not found."
  }
]
```

The response contains results for each request in order.

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| errorCode | Int32 | (Required) Error code. `0` = success. |
| errorMsg | String | (Required) Error message |

---

## 2.6.10 lockFileByRange — Lock/Unlock Machine Program Editing

Currently supports locking/unlocking program editing for Mitsubishi and Fanuc systems, for program numbers in the ranges 8000–8999, 9000–9999, and 8000–9999. Some systems prevent modification after locking, but the program may still be deleted from the machine side.

```
GET  /api/cnc/lockFileByRange?machineID=MACHINEID&isLock=ISLOCK&lockStart=LOCKSTART&lockEnd=LOCKEND
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| isLock | Bool | (Required) Target lock state. `true` = lock, `false` = unlock. |
| lockStart | Int32 | (Required) Starting program number of the lock/unlock range |
| lockEnd | Int32 | (Required) Ending program number of the lock/unlock range |

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

## 2.6.11 createDir — Create Machine Directory

```
GET  /api/cnc/createDir?machineID=MACHINEID&dirName=DIRNAME&dirAtCNC=DIRATCNC&subDir=SUBDIR
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| dirName | String | (Required) Name of the directory to create |
| dirAtCNC | String | Parent directory path. If not specified, defaults to the default root directory path (see Table 55). |
| subDir | String | Parent subdirectory path. Combined with root if `dirAtCNC` is not provided. Ignored if both are provided. |

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

**Table 57 — Create/Delete Directory Support by CNC System**

| File Server Type | System (Model) | Create/Delete Directory |
|-----------------|----------------|-------------------------|
| Machine Storage | Brother [S Series] | Yes |
| Machine Storage | Delta [Universal] | Yes |
| Machine Storage | Fanuc [0i-D, 0i-F, 30i, 31i, 32i, 35i, Power Motion i-A] | Yes |
| Machine Storage | GSK [All Models] | No |
| Machine Storage | Haas [Universal, MT-CONNECT] | Yes |
| Machine Storage | Heidenhain [All Models] | Yes |
| Machine Storage | Mitsubishi [M70/M700 L, M70/M700 M, M80/M800 L, M80/M800 M] | ★ CF card / SD card only (path: IC1) |
| Machine Storage | Mock (Simulated Machine) | Yes |
| Machine Storage | Okuma [All Models] | Yes |
| Machine Storage | Rexroth [OPC UA] | Yes |
| Machine Storage | Siemens [All Models] | Yes |
| Machine Storage | Other models supporting program transfer | No |
| FTP Server | | Yes |
| Shared Folder | | Yes |
| Shared Folder (Win XP) | | Yes |
| Wireless Transfer Box | | Yes |
| Gateway File Server | | Yes |

Yes: Supported; No: Not supported

---

## 2.6.12 deleteDir — Delete Machine Directory

Only empty directories can be deleted. Delete all files and subdirectories within the directory before calling this interface.

```
GET  /api/cnc/deleteDir?machineID=MACHINEID&dirName=DIRNAME&dirAtCNC=DIRATCNC&subDir=SUBDIR
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| dirName | String | (Required) Name of the directory to delete |
| dirAtCNC | String | Parent directory path. If not specified, defaults to the default root directory path (see Table 55). |
| subDir | String | Parent subdirectory path. Combined with root if `dirAtCNC` is not provided. Ignored if both are provided. |

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

For supported CNC systems, refer to Table 57 — Create/Delete Directory Support by CNC System.

---

## 2.6.13 backupFiles — Backup Machine Files

This interface packages the specified files, or all files within a directory (including subdirectories and their contents), into a ZIP archive and returns it as a stream. The ZIP archive structure is: first level — `"BackupFiles_<date-time>"`, second level — one folder per machine named `"<machineID>_<machineName>"`, inside which the specified files and directories are preserved in their original folder structure.

```
POST  /api/cnc/backupFiles
```

**Request Body Example** (application/json)

```json
[
  { "machineID": "1010", "fileName": "08000" },
  { "machineID": "1011", "DirAtCNC": "dir1", "includeSubDir": false },
  { "machineID": "1012", "fileName": "sample.NC", "SubDir": "Dir/Prog" }
]
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| fileName | String | Target file name. Refer to file names from `readProgramList` or `readAllPrograms`. Some systems require the file extension. If `fileName` is not provided, all files in the directory specified by `dirAtCNC` or `subDir` are backed up. |
| dirAtCNC | String | Target directory path. If not specified, defaults to the default root directory path (see Table 55). |
| subDir | String | Target subdirectory path. Combined with root if `dirAtCNC` is not provided. Ignored if both are provided. |
| includeSubDir | Bool | When `fileName` is not specified (backing up an entire directory), if `true`, all files in subdirectories are also included. Default is `true`. |

**Response Example** (`application/octet-stream`)

```
PK gx BackupFiles_2024.03.07.12.04.46…
```

The response body contains the ZIP archive delivered as a stream.

If any operation in the request fails, the task is aborted and the error for that request is returned, e.g.:

```json
{
  "errorCode": 10003,
  "errorMsg": "Machine ID does not exist.",
  "statusMsg": "Invalid MachineID (1011)"
}
```

---

## 2.6.14 selectProgram — Select Program

Selects the specified program as the main program on the machine.

```
GET  /api/cnc/selectProgram?machineID=MACHINEID&fileName=FILENAME&dirAtCNC=DIRATCNC&subDir=SUBDIR
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| fileName | String | (Required) Target file name. Refer to file names from `readProgramList` or `readAllPrograms`. Some systems require the file extension. |
| dirAtCNC | String | Target directory path. If not specified, defaults to the default root directory path (see Table 55). |
| subDir | String | Target subdirectory path. Combined with root if `dirAtCNC` is not provided. Ignored if both are provided. |

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

**Supported Devices**

| System (Model) | Notes |
|----------------|-------|
| Brother [All Models] | Machine must be in Auto Run or Run-in-Edit mode; no program may be currently running. |
| Fanuc [0i-D, 0i-F, 30i, 31i, 32i, 35i, Power Motion i-A] | Machine must be in Auto or Edit mode. |
| Mock (Simulated Machine) | Always returns SUCCESS but does not change the current program. |

---

## 2.6.15 readAllPrograms — Browse All Files

Searches for all program files in the specified directory on the machine (including subdirectories) and returns the file names and their directory paths.

```
GET  /api/cnc/readAllPrograms?machineID=MACHINEID&dirAtCNC=DIRATCNC&subDir=SUBDIR
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| dirAtCNC | String | Target directory path. If not specified, defaults to the default root directory path (see Table 55). |
| subDir | String | Target subdirectory path. Combined with root if `dirAtCNC` is not provided. Ignored if both are provided. |

**Response Example**

```json
{
  "programs": [
    {
      "dirAtCNC": "/",
      "fileName": "SAMPLE"
    },
    {
      "dirAtCNC": "1",
      "fileName": "sample1.nc"
    },
    {
      "dirAtCNC": "1/1",
      "fileName": "sample2.nc"
    }
  ]
}
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| programs | Object[] | (Required) Array of program file objects |
| fileName | String | Program file name. Some systems (e.g., GSK, Siemens) include the file extension. |
| dirAtCNC | String | Directory path where the program file is located |

---

## 2.6.16 searchFile — Search for a Machine File

Searches for a specified file name in the given directory (including subdirectories) and returns the path of the directory containing the file, along with all files and subdirectories in that directory.

```
GET  /api/cnc/searchFile?machineID=MACHINEID&fileName=FILENAME&dirAtCNC=DIRATCNC&subDir=SUBDIR
```

**Request Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| machineID | String | (Required) Target machine identifier; see section 1.1.1 machineID — Machine Identifier |
| fileName | String | (Required) Target file name. Refer to file names from `readProgramList` or `readAllPrograms`. Some systems require the file extension. |
| dirAtCNC | String | Search root directory path. If not specified, defaults to the default root directory path (see Table 55). |
| subDir | String | Search root subdirectory path. Combined with root if `dirAtCNC` is not provided. Ignored if both are provided. |

**Response Example**

```json
{
  "dirAtCNC": "Dir/Prog",
  "programs": [
    "1111",
    "2322",
    "3222"
  ],
  "subDirs": [
    "dir1",
    "dir2"
  ]
}
```

**Response Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| dirAtCNC | String | Directory path where the target file was found |
| programs | String[] | List of programs (files) in the directory containing the target file |
| subDirs | String[] | List of subdirectories in the directory containing the target file |
