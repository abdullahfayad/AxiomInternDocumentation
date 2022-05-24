# Part 1: Get API

## Brief

We will create a GET API to assure sensor data collection in the local MySQL Database.

## Steps

##### Using PHP:

1. Create a PHP file using Visual Studio Code or any other editor in your MAMP/htdocs or XAMPP/htdocs folder (or WWW folder for linux users).
2.  Modify the code below with your information.

## PHP File

```python
<?php
  class dataNode{
    public $link='';
    function __construct($replace_with_your_var){
      $this->connect();
      $this->storeInDB($replace_with_your_var);
    }
    function connect(){
      $this->link = mysqli_connect('replace_with_your_host_name','replace_with_your_username','replace_with_your_password', 'replace_with_your_port') or die('Cannot connect to the DB');
      mysqli_select_db($this->link,'replace_with_your_database_name') or die('Cannot select the DB');
    }
    function storeInDB($your_var){
      $query = "Insert into replace_with_your_table_name(replace_with_your_attribute) Values(".$replace_with_your_var.")";
      $result = mysqli_query($this->link,$query) or die('Errant query:  '.$query);
    }
  }
  if($_GET[replace_with_your_var] != ''){
    $dataNode=new dataNode($_GET[replace_with_your_var]);
  }
?>
```

<img style="float:right; " src="../../../Images/problem.png" width=50> Note: The above php code doesn’t work with POST API.

##### Using Arduino IDE:

1. Install the following libraries:
    - ESP8266WiFi
    - ESP8266HTTPClient
    - WiFiClient
  By going to Tools > Manage Libraries.
2. Compile the code below to your NodeMCU after modifying it with your information and adding the [output](../../Part%20I:%20Collecting%20Data/Collecting%20Data.md) function.


### Arduino Code

```python
#include <ESP8266WiFi.h>
#include <ESP8266HTTPClient.h>
#include <WiFiClient.h>

const char* ssid = "replace_with_your_ssid";
const char* password = "replace_with_your_password";

String serverName = "http://replace_with_your_local_ip_address/replace_with_your_php_file_name";

void setup() {
  Serial.begin(115200);
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
  String res= output();
    if(WiFi.status()== WL_CONNECTED){
      HTTPClient http;
      String serverPath = serverName + "?replace_with_your_var='"+res+"'";
      
      http.begin(serverPath.c_str());
      
      int httpResponseCode = http.GET();
      
      if (httpResponseCode>0) {
        Serial.print("HTTP Response code: ");
        Serial.println(httpResponseCode);
        String payload = http.getString();
        Serial.println(payload);
      }
      else {
        Serial.print("Error code: ");
        Serial.println(httpResponseCode);
      }
      http.end();
    }
    else {
      Serial.println("WiFi Disconnected");
    }
}
```

<img style="float:right; " src="../../../Images/problem.png" width=50> Note: Get method doesn’t allow special characters like space.

# Part 2: Post API

## Brief

We will create a POST API to assure sensor data collection in local MySQL Database.

## Steps

1. Create a php file using Visual Studio Code or any other editor in your mamp/htdocs or xampp/htdocs folder (or www folder for linux users).
2.  Modify the code below with your information.


## PHP File

```python
<?php
    $servername = "replace_with_your_server_name";
    $username = "replace_with_your_username";
    $password = "replace_with_your_password";
    $dbname = "replace_with_your_database_name";
    $conn = new mysqli($servername, $username, $password, $dbname);
    if ($conn->connect_error) {
        die("Database Connection failed: " . $conn->connect_error);
    }
    if(isset($_POST['replace_with_your_var']))
    {
        $status = $_POST['replace_with_your_var'];
        $sql = "INSERT INTO replace_with_your_table_name(replace_with_your_attribute) VALUES('".$replace_with_your_var."')";
        if ($conn->query($sql) === TRUE) {
            echo "OK";
        } else {
            echo "Error: " . $sql . "<br>" . $conn->error;
        }
     }else{
         echo "Empty Params";
     }
    $conn->close();
?>

```
<img style="float:right; " src="../../../Images/problem.png" width=50> Note: The above php code works with both POST and GET APIs. All you have to do is replace `$_POST` with `$_GET`.

Compile the code below to your NodeMCU after modifying it with your information and adding the output function.

### Arduino Code

```python

#include <ESP8266WiFi.h>
#include <WiFiClient.h> 
#include <ESP8266WebServer.h>
#include <ESP8266HTTPClient.h>

const char* ssid = "replace_with_your_ssid";
const char* password = "replace_with_your_password";

String serverName = "http://replace_with_your_local_ip_address/replace_with_php_file_name";

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
  
    String postData = "replace_with_your_var="+data;
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
## Database Mockups

### Flame Sensor

<img style="float:right; " src="../1. Local MySQL Database/Local MySQL Database/flamesensor.png">

### Air Quality Sensor

<img style="float:right; " src="../1. Local MySQL Database/Local MySQL Database/airquality.jpeg">


### Temperature and Humidity Sensor

<img style="float:right; " src="../1. Local MySQL Database/Local MySQL Database/temperatureandhumidity.jpg">

