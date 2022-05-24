# Azure MySQL Database 

## Brief

We will insert data into Azure MySQL Database using Local API. We will also create a trigger that will insert data into another table.

## Steps

1. After creating a MySQL Database on Azure, navigate to your database and then to your connection security under settings.
    - <img style="float:right; " src="../../../Images/problem.png" width=50> Be careful not to create an sql database instead of MySQL.
    - <img style="float:right; " src="../../../Images/problem.png" width=50> Be careful not to allocate a high number of cores and storage while creating your    database which can lead to a huge payment amount per month (more than $200). Set all the allocated resources to the minimum amount which will lead to a payment of almost $25 per month instead.
        
2. Press on + Add 0.0.0.0 - 255.255.255.255
3. We open MySQL workbench and create a new MySQL connection.
4. For the connection, choose a connection name of your liking.
5. Keep the port as is (3306).
6. You can find your other credentials in the azure website under connection strings, where hostname will be in the form “abc.mysql.database.azure.com", and username will be in the form `abc@abcd`.
7. As for your password, it will be the password you chose when you created your database. To enter your password, click on Store in keychain.
8. Finally, click on test connection to make sure that everything is okay.
9. You have now successfully connected to your azure database. Double click on the connection to start working.
10. On the azure website, go to the directory of your database, then enter server parameters under settings.
11. Search for “log_bin_trust_function_creators” and turn it on.
12. Modify the code below according to your information.
## MySQL WorkBench Code
```python
-- Create a database
DROP DATABASE IF EXISTS axiomworks;
CREATE DATABASE axiomworks;
USE axiomworks;

-- Create a table and insert rows
CREATE TABLE log_insert (date datetime NOT NULL DEFAULT CURRENT_TIMESTAMP,status int NOT NULL);
CREATE TABLE temp_logs (data int NOT NULL);

-- Triger:
DELIMITER $$
 
CREATE TRIGGER onInsertion
    AFTER INSERT
    ON temp_logs FOR EACH ROW
BEGIN
       INSERT INTO log_insert (status) VALUES (new.data);
END$$   
 
DELIMITER ;

-- Insert dummy data
INSERT INTO temp_logs (data) VALUES (0);
INSERT INTO temp_logs (data) VALUES (1);
INSERT INTO temp_logs (data) VALUES (1);

-- Read
SELECT * FROM temp_logs;
SELECT * FROM log_insert;
```
13. Download the DigiCertGlobalRootG2 SSL certificate through this [link](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)
14. Place the downloaded certificate in the same directory as the php file (both should be in the htdocs file).
15. I renamed the certificate to “cert.crt.pem” just to make it less lengthy.
16. In the php file below, change the `host`, `username`, `password`, and `db_name` according to your connection. 

## PHP Code

```python
<?php
$host = 'your_host_name';
$username = 'your_username';
$password = 'your_password';
$db_name = 'your_Database_name';

//Initializes MySQLi
$conn = mysqli_init();

mysqli_ssl_set($conn,NULL, "/cert.crt.pem",NULL, NULL, NULL);

// Establish the connection
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL);

//If connection failed, show the error
if (mysqli_connect_errno($conn))
{
    die('Failed to connect to MySQL: '.mysqli_connect_error());
}
 if(isset($_POST['new']))
     {
         $status = $_POST['new'];
         printf($status);
if ($stmt = mysqli_prepare($conn, "INSERT INTO table_name (attribute) VALUES('".$status."')"))
{
    mysqli_stmt_execute($stmt);
    printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
    mysqli_stmt_close($stmt);
}
     }
?>
```
17. In the Arduino code below, fill in your ssid and password.

## Arduino Code

```python
#include <ESP8266WiFi.h>
#include <WiFiClient.h> 
#include <ESP8266WebServer.h>
#include <ESP8266HTTPClient.h>

const char* ssid = "Your_ssid";
const char* password = "Your_password";

String serverName = "http://your_ip_address/your_php_file_name";

void setup() {
  Serial.begin(9600);
  WiFi.begin(ssid, password);
  Serial.println("Connecting");
  while(WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
 }
  Serial.println("");
  Serial.print("Connected to WiFi network with IP Address: ");
  Serial.println(WiFi.localIP());
}

void loop() {
  String data= output();
  Serial.println(data);
  if(WiFi.status()== WL_CONNECTED){
    HTTPClient http;
    http.begin(serverName);
    String postData = "new="+data;
    http.addHeader("Content-Type", "application/x-www-form-urlencoded");    
    int httpCode = http.POST(postData);
    String payload = http.getString(); 
    Serial.println(payload);
    Serial.print("HTTP Response code: ");    
    Serial.println(httpCode);
    http.end();
    }
    else {
      Serial.println("WiFi Disconnected");
    }
    delay(2000);
}
```
18. Finally, add the [output](../../Part%20I:%20Collecting%20Data/Collecting%20Data.md) function to your code and compile to your NodeMCU.
## Database Mockups

### Flame Sensor

<img style="float:right; " src="../2. Microsoft Azure/Microsoft Azure/flamesensor.png">

### AirQuality Sensor
