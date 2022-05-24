# Collecting Data

Compile and upload the code below to your NodeMCU using Arduino IDE. Your sensor data will be printed to the serial monitor.

## Flame Sensor

```python
String output() {
    int t = digitalRead(D0);
    int res = 0;
    if ( t == 0 ) {        
        res = 1;
    }
    else {
        res = 0;
    }
    return String(res);
}
void setup() {
   Serial.begin(115200);
}
void loop() {
   String data = output();
   Serial.println(data);
   delay(500);
}
```
<img style="float: right;" src="../../Images/problem.png" width=50> In the code above, the '1' represents the output "Fire Detected", whereas the '0' represents the output "Fire not Detected".

## Air Quality Sensor

```python
String output() {
    int t = analogRead(0);
    String res = String (t);
    return res;
}
void setup() {
   Serial.begin(115200);
}
void loop() {
   String data = output();
   Serial.println(data);
   delay(500);
}

```

## Temperature and Humidity Sensor

```python
#include "DHT.h"

DHT dht(D4, DHT11);

float Temperature;
float Humidity;

void setup()
{
  Serial.begin(115200);
  pinMode(D4, INPUT);
  dht.begin();
}

void loop()
{
  Temperature = dht.readTemperature();
  Humidity = dht.readHumidity();
  Serial.print("Temperature = ");
  Serial.println(Temperature);
  Serial.print("Humidity = ");
  Serial.println(Humidity);
  delay(1000);
}
```
