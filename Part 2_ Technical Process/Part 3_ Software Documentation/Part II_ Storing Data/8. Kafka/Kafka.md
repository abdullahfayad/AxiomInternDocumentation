# Kafka

## Brief

Send sensor data and current timestamp to Kafka topic using nodejs.

## Steps

#### Install Kafka and Zookeeper:

###### For MacOS:
1. Open terminal.
2. Install homebrew if you haven't done so already.
3. Install kafka using the following command: brew install kafka.
4. Zookeeper should be installed with kafka. If it wasn't installed for any reason, install it using the following command: brew install zookeeper.
###### For Windows:
1. Download Apache Kafka [here](https://www.apache.org/dyn/closer.cgi?path=/kafka/2.7.0/kafka_2.13-2.7.0.tgz), then extract the downloaded file to another, rename it Kafka and place it in your C:/ directory.
2. Go to kafka->config->server.properties and change the log.dirs to log.dirs = C:/kafka/kafka-logs
3. Download Apache Zookeeper [here](https://www.apache.org/dyn/closer.lua/zookeeper/zookeeper-3.7.0/apache-zookeeper-3.7.0-bin.tar.gz), then extract the downloaded file to another, rename it Zookeeper and place it in your C:/ directory.
4. Go to Zookeeper->conf->zookeeper.cfg rename it zoo.cfg and change the dataDir to dataDir=C: /zookeeper/data.
5. Now before you start you need to make sure that you have java installed on your pc and that it is global that means you can reach it from any directory, in order to do that go to control panel -> System -> advanced system settings and click on environment variables, now click new, in variable name write 
6. JAVA_HOME and in variable value browse the path where jdk of java is located, now go to path and add this path also to the variables value.

#### Create a topic:

1. Start zookeeper using the following command: `zkServer start (macOS) zkserver` (windows)
2. Start kafka using the following command: `kafka-server-start /usr/local/etc/kafka/server.properties` (macOS) `C:\kafka\bin\windows\kafka-server-start.bat          C:\kafka\config\server.properties`  (windows)
3. Open another terminal window.
4. Create a topic: `kafka-topics --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test` (you can change the replication factor, number of partitions, and 'test' which is the name of the topic)

#### Send Sensor data and current timestamp using two methods:

##### Method 1:

1. Download node.js [here](https://nodejs.org/en/download/)
###### Arduino Code:
```python
#include <ESP8266WiFi.h>
#include <ESP8266HTTPClient.h>
#include <WiFiClient.h>

String getdata(){
 int val = analogRead(A0);
 String airquality  = String (val);
 return airquality;
}

void setup() {

  Serial.begin(9600);                 
  WiFi.begin("Your Network Name", "network password");   
  while (WiFi.status() != WL_CONNECTED) 
    delay(500);
    Serial.println("Waiting for connection");  
}

void loop() {
  
if (WiFi.status() == WL_CONNECTED) { 
Serial.println("Connected Working");
HTTPClient http;  
http.begin("your ip/your port nodejs listening to");  
http.addHeader("Content-Type", " application/x-www-form-urlencoded");

String payload="airquality=" + getdata();
Serial.println(payload);

int httpCode = http.POST(payload);
  if (httpCode>0) {
        Serial.print("HTTP Response code: ");
        Serial.println(httpCode);
        String payload = http.getString();
        Serial.println(payload);
      }
      else {
        Serial.print("Error code: ");
        Serial.println(httpCode);
      }
  
http.end();   
}
 
delay(5000);    
  
}
```
2. Create a folder in your desired location, open your terminal in this location and type npm init, keep pressing enter, then type ```python npm install express ``` this library enable you to listen to a specified port where the data is sent to, then type ```python npm install kafka-node```.
3. Now create two files in this folder: the producer file that gets the data by listening to the specified port and sends them, and the consumer file that gets the data and stores them in the topic

###### Consumer.js:

```python
const kafka = require('kafka-node');
const Consumer = kafka.Consumer;
const client = new kafka.KafkaClient();
const consumer = new Consumer(
     client,
        [
            { topic: 'topicname', partitions: 0 }, { topic: 'topicname', partitions: 1 }
        ],
        {
            autoCommit: false
        }
    );
 
consumer.on('message', function (message) {
    console.log(message);
});
```


4. Now run the consumer by opening the cmd in the   nodejs   location   and   type   nodeconsumer.js  to listen to any producer that writes in the topic and reads the data from it

###### Producer.js

```python
var express = require('express');
var app = express(); 
var bodyParser = require('body-parser');
const kafka = require('kafka-node');
//create a producer
const Producer = kafka.Producer;
const client = new kafka.KafkaClient();
const producer = new Producer(client);
//displayed on arduino serial monitor to confirm the connection
app.get('', function (req, res) { 
res.send("Hello from Server"); 
})

app.use(bodyParser.urlencoded({ extended: false })) 
app.post('', function(req, res) {    
//displayed on arduino serial monitor to confirm the data collection
res.send('Got the airquality data, thanks..!!');
//json array got from arduino stringfied to be displayed as string
var result = (JSON.stringify(req.body));
//extracting the json array as json object
var jsonObj = JSON.parse(result);
//extracting the json object by the key
var sensor  = JSON.parse(jsonObj['airquality']); 
//extreacting the values of the json array to be produced
var val     = sensor['sensor'];
var time    = sensor['time'];


 const payloads = [
    
     { topic: topic_name, messages: val  ,  partition: 0}, 
     { topic: topic_name, messages: time ,  partition: 1}
                  ];
//producing the values
 producer.send(payloads, function(error, data) {
     if (error) {
         console.error(error);
     } else {
         console.log(data);
     }})
}) 
//listening to the port where the data is sent to
var server = app.listen(80, function () {    
var host = server.address().address    
var port = server.address().port
console.log("Example server listening at localhost:%s", host, port) })
```
5. Now run the producer file by opening a new cmd in the nodejs folder location and type node publish.js
6. Now what is written in the producer file will be shown in the consumer.


##### Method 2:

1. Install node.js
2. Open terminal and install express using the following command: nmp install express.
3. Install kafkajs using this command in terminal: npm install kafkajs.

###### Using Arduino IDE:

4. Modify your wifi credentials and ip address.
5. We will use the `get_Time` function to get the current timestamp, and the "output" function to get the sensor output.

<img style="float:right; " src="../../../Images/problem.png" width=50> We are using the arduinojson library in version 6. 

```python
#include <ESP8266WiFi.h>
#include <NTPClient.h> 
#include <WiFiUdp.h>
#include <ArduinoJson.h>

const char* ssid     = "Your_ssid";
const char* password = "Your_password";

const char* host = "Your_ip_address";

const long utcOffsetInSeconds = 0;
WiFiUDP ntpUDP;
NTPClient timeClient(ntpUDP, "pool.ntp.org", utcOffsetInSeconds);

String output() {
    int t = digitalRead(D0);
    String res ="noSensorData";
    Serial.println(t);
    if (t==0) {        
        res= "firedetected";
    }
    else {
        res = "firenotdetected";
    }
    return res; 
}
unsigned long getTime() {
  timeClient.begin();
  timeClient.update();
  long unsigned now = timeClient.getEpochTime();
  return now;
}
void setup() {
  Serial.begin(115200);
  delay(10);
  Serial.println();
  Serial.println();
  Serial.print("Connecting to ");
  Serial.println(ssid);

  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
   delay(500);
   Serial.print(".");
  }

  Serial.println("");
  Serial.println("WiFi connected");  
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
}

void loop() {
 delay(5000);
 Serial.print("connecting to ");
 Serial.println(host);

 WiFiClient client;
 const int httpPort = 8081;
 if (!client.connect(host, httpPort)) {
  Serial.println("connection failed");
  return;
 }
    StaticJsonDocument<200>doc;
    doc["Timestamp"]=getTime();
    doc["Status"]=output();
    char buffer[256];
    serializeJson(doc,buffer);
    String data = "flame= "+String(buffer);
   Serial.print("Requesting POST: ");
   client.println("POST / HTTP/1.1");
   client.println("Host: server_name");
   client.println("Accept: */*");
   client.println("Content-Type: application/x-www-form-urlencoded");
   client.print("Content-Length: ");
   client.println(data.length());   
   client.println();
   client.print(data);

unsigned long timeout = millis();
 while (client.available() == 0) {
 if (millis() - timeout > 5000) {
  Serial.println(">>> Client Timeout !");
  client.stop();
  return;
 }
}
while(client.available()){
  String line = client.readStringUntil('\r');
  Serial.print(line);
 }

 Serial.println();
 Serial.println("closing connection");
}
```
###### Javascript Code

1. Modify the code below with your topic name.
```python
const { Kafka } = require("kafkajs");
const clientId = "my-app"
const brokers = ["localhost:9092"]
const topic = "topicname"
var express = require('express');
var app = express(); 
var bodyParser = require('body-parser') 
app.get('/', function (req, res) { 
res.send("Hello from Server"); 
}) 
app.use(bodyParser.urlencoded({ extended: false })); 
app.post('/', function(req, res) {    
res.send('Got the flame sensor data, thanks..!!');
var x=JSON.parse(req.body["flame"])
var Timestamp = x["Timestamp"]
var Status = x["Status"];
run().then(() => console.log("Done"), err => console.log(err));
async function run() {
    const kafka = new Kafka({ clientId, brokers })
    const consumer = kafka.consumer({ groupId: clientId })
  await consumer.connect()
      await consumer.subscribe({ topic })
      await consumer.run({
          eachMessage: ({ message }) => {
              console.log(`received message: ${message.value}`)
          },
      })
  
    const producer = kafka.producer();
  await producer.connect()
      let i = 0
  
      setInterval(async () => {
          try {
              await producer.send({
                  topic,
                  //partition:i,
                  messages: [
                      {
                          key: String(i),
                          partition:0,
                          value: String(Timestamp),
                      },
                      {
                        key: String(i),
                        partition:1,
                        value: Status,
                    },
                  ],
              })
  
              console.log("writes: ", i)
              i++
          } catch (err) {
              console.error("could not write message " + err)
          }
      }, 1000)
  }

}) 
var server = app.listen(8081, function () {    
var host = server.address().address    
var port = server.address().port;
console.log("Example server listening at localhost:%s", host, port) })
```

<img style="float:right; " src="../../../Images/problem.png" width=50> To see your sent data on the partitions you can download a tool called Offset explorer [here](https://www.kafkatool.com/download.html)
