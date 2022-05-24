# Local MySQL Database

## Brief

We will insert sensor data into local MySQL Database using python libraries and Spyder provided by Anaconda.

## Steps

1. Install python libraries (mysql.connector and pyserial)	
2. Create a new database and table on phpMyAdmin using MAMP/XAMPP.
3. Run your [arduino code](../../Part%20I:%20Collecting%20Data/Collecting%20Data.md) followed by the python code below to retrieve your sensor data and insert them to the database.

<img style="float:right; " src="../../../Images/problem.png" width=50> Note: You may face difficulties installing python libraries manually (pyserial /mysql connector). If so, install them through the terminal.

## Python File

In the code below: 
- Modify `your_comport_name` with your own.
- Modify your MYSQL Database initials.

```python
import mysql.connector
import serial
ser = serial.Serial('your_comport_name', 115200)
mydb = mysql.connector.connect(
  host = "your_host_name",
  username = "your_username",
  password = "your_password",
  database = "your_Database"
)
mycursor = mydb.cursor()
while True:
    val = ser.readline()
    val = val.decode('utf-8')
    print(val)
    sql = "INSERT INTO table_name(attributes) VALUES ('"+val+"')"
    mycursor.execute(sql)
         mydb.commit()

```
## Database Mockups

### Flame Sensor

<img style="float:right; " src="../1. Local MySQL Database/Local MySQL Database/flamesensor.png">

### Air Quality Sensor

<img style="float:right; " src="../1. Local MySQL Database/Local MySQL Database/airquality.jpeg">


### Temperature and Humidity Sensor

<img style="float:right; " src="../1. Local MySQL Database/Local MySQL Database/temperatureandhumidity.jpg">
