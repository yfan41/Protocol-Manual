## VI. Simulated Machine Test

If you need to quickly test the communication protocol without an actual machine connection, you can add a simulated machine in the **Machine Configuration** page of the gateway management interface. Set the local IP to 127.0.0.X; the machine ID and slave station ID default to X, where X can be set to 1–255 (see the *User Manual*, section 5.3 Machine Configuration). Activate the machine and enable its automatic data collection option. Restart the service on the home page to test the HTTP protocol. To test additional communication protocols, enable the corresponding protocol and configure its parameters in the **Communication Configuration** page of the gateway management interface, then restart the service on the home page. When putting the gateway into actual use, delete the simulated machine to avoid slave station ID conflicts.

---

## VII. Frequently Asked Questions

### 7.1 HTTP communication returns "This site can't be reached (ERR_CONNECTION_REFUSED)"

Check whether the computer you are using can access the gateway management page. If it cannot, verify that the gateway is powered on and that the network connection between your computer and the gateway is functioning properly.

---

### 7.2 HTTP communication returns `"errorCode": 3`

Use the **Interface Test** function on the gateway management page to check the communication between the gateway and the machine (see the *User Manual*, section 5.8 Interface Test). If the target device cannot be found in the "Select Machine" dropdown on the **Interface Test** page, the device has not been added or activated. Refer to *User Manual* section 5.3 Machine Configuration to add and activate the machine. After completing the configuration, you must click **Restart Service** on the **Home** page.

If the target device is found on the **Interface Test** page and clicking **Read Device** returns "Read Failed," follow the troubleshooting instructions shown below the "Read Failed" message.

---

### 7.3 No data received when using MODBUS or MQTT communication

Check the machine's automatic data collection settings (see the *User Manual*, section 5.3 Machine Configuration). After completing the configuration, you must click **Restart Service** on the **Home** page.

Check the task configuration (see the *User Manual*, section 5.5 Task Configuration). After completing the configuration, you must click **Restart Service** on the **Home** page.

If no data is still received, follow the steps described in section 6.2.

---

### 7.4 Unable to obtain valid data at a given address when using MODBUS communication

A common cause is a MODBUS client configuration issue. The gateway's MODBUS server uses 0-based addressing. If your client uses 1-based addressing, add 1 to all address values. For testing, it is recommended to use Modbus Poll software to read address data — when setting the address range, **do not check "(Base 1)"**, so that 0-based addressing is used.

Some CNC machines with older system software versions do not support certain data collection items; the corresponding address values remain 0.

---

### 7.5 Machine data is received but no machine group data is received with any communication protocol

Check that the machine group is correctly activated and that the machine group task has been enabled (see the *User Manual*, section 5.4 Machine Group Configuration). After completing the configuration, you must click **Restart Service** on the **Home** page.

Check the task configuration (see the *User Manual*, section 5.5 Task Configuration). After completing the configuration, you must click **Restart Service** on the **Home** page.

---

### 7.6 Failure when listing files or sending/receiving/deleting files via API, while the gateway program transfer page works normally

The path or filename contains special characters or spaces (unsafe characters) that must be URL-encoded. For example:

- Path `TNC:\nc_prog` must be encoded as `TNC%3A%5Cnc_prog`
- `Sinumerik/FileSystem/Part Program` must be encoded as `Sinumerik%2FFileSystem%2FPart%20Program`

In most cases, browsers and testing tools URL-encode values automatically. However, manual encoding may be required when calling the interface from certain software.
