## V. Database Communication

Once database communication is enabled and the automatic data collection task for the target machine or machine group is activated (see the *User Manual*, section 5.3.1 Add Machine — task settings for each machine type, and section 5.4.1.2 Machine Group Task Settings), the gateway will automatically write the collected task results into the specified database. Supported database types currently include: InfluxDB v2.x, MySQL, SQL Server, PostgreSQL, and others. Refer to *User Manual* section 5.6.4 Database Configuration for instructions on configuring the database service.

Database table names follow the format **[table prefix][data class]**, where the table prefix is configured on the gateway communication configuration page (see *User Manual*, section 5.6.4 Database Configuration). The table names are the same as the `type` data class names used in MQTT communication (see section 1.2 Data Description). The following shows example table names when no table prefix is set and the save mode is log mode:

| Table Name |
|------------|
| alarmhistory |
| alarmlog |
| cncstatus |
| count |
| currenttoolnumber |
| cycle |
| energyconsum |
| feedandspindle |
| groupcount |
| groupcumulative... |
| groupoee |
| load |
| loghistory |
| machine |
| oee |
| offset |
| plc |
| position |
| programblock |
| programinfo |
| timedata |
| toollife |

The fields in each database table correspond to the data tags (`tag`) and data fields (`field`) of the matching data class `type` (see section 1.2 Data Description). Using the `cncStatus` table as an example, its columns include:

| Field Name | Data Type |
|------------|-----------|
| id | BIGINT (auto-increment) |
| cncStatus | TEXT |
| adjustedStatus | TEXT |
| alarmStatus | TEXT |
| alarmLevel | TEXT |
| offTime | BIGINT |
| waitTime | BIGINT |
| emergencyTime | BIGINT |
| autoRunTime | BIGINT |
| manualTime | BIGINT |
| channel | VARCHAR(128) |
| machineID | VARCHAR(128) |
| time | DATETIME |
| uid | VARCHAR(128) |
