# Google Spreadsheet

## Brief

Send data from NodeMCU to google spreadsheet.

## Steps

1. Open your google drive account and create a new blank google spreadsheet.
2. Rename it with a name of your liking. On the very first row of your spreadsheet, you should add the names of the fields you will be recording. For instance I chose Date, Time, and Status.
3. Save your sheet ID, which you will find in the url of your spreadsheet: https://docs.google.com/spreadsheets/d/ `1HmE5xzvDlTq6HMCQ3D_hgwWTEqtSUWGfnzhho_hvrt8` /edit#gid=0 By removing the `https://docs.google.com/spreadsheets/d/` from the beginning and the `/edit#gid=0` from the end.
4. After that, go to Tools-> Script editor.
5. Rename the script to the same name of the spreadsheet you created.
6. Insert the script found below.
7. Replace the var sheet_id with your spreadsheet id.
8. Modify the code to include other fields that you want to record if any.
9. Finally, press Deploy-> New Deployment.
10. Select the type `Web app` and change the access type to anyone, even anonymous, then click deploy.
11. If it is your first time, you will be asked for authorization.
12. Click on review permissions.
13. Then another window will pop up, click on advanced.
14. Choose Go to (file name)(unsafe).
15. Your new deployment is successfully created. Copy the Deployment ID and URL for later use.

    <img style="float:right; " src="../../../Images/problem.png" width=50> Every time you modify the script, you will have to deploy again.
   
16. In case the deployment ID wasn’t given to you, you can still find it in the url.
17. The url copied should look something like: https://script.google.com/macros/s/ `AKfycbytqRqu8yFp3yrvbdx3NQJIbKAP8iGC-x-irYGWcJyxCVZsaxLIIPxWmQ` /exec
which is in the form: https://script.google.com/macros/s/DEPLOYMENTID/exec
18. You can try your url to try and push some data into the spreadsheet.
19. To do this, add to the end of your url the following: 
https://script.google.com/macros/s/AKfycbytqRqu8yFp3yrvbdx3NQJIbKAP8iGC-x-irYGWcJyxCVZsaxLIIPxWmQ/exec? `status=flame` where status is the name of my variable. If you have more than one variable in your table, put a `&` between each two variables.
20. Enter the new url, you should now find the added value in your spreadsheet.


###### Google Spreadsheet Script

```python
function doGet(e) { 
  Logger.log( JSON.stringify(e) );  // view parameters
  var result = 'Ok'; // assume success
  if (e.parameter == 'undefined') {
    result = 'No Parameters';
  }
  else {
// Spreadsheet ID
    var sheet_id = '1HmE5xzvDlTq6HMCQ3D_hgwWTEqtSUWGfnzhho_hvrt8';  
// get Active sheet  
    var sheet = SpreadsheetApp.openById(sheet_id).getActiveSheet();  
    var newRow = sheet.getLastRow() + 1;            
    var rowData = [];
    d=new Date();
    d.setHours( d.getHours() + 7 );
    rowData[0] = d; // Timestamp in column A
    rowData[1] = d.toLocaleTimeString(); // Timestamp in column A
    for (var param in e.parameter) {
      Logger.log('In for loop, param=' + param);
      var value = stripQuotes(e.parameter[param]);
      Logger.log(param + ':' + e.parameter[param]);
      if (param=='status'){
        rowData[2] = value; //Value in column A
        result = 'Written on column A';
      }
      else {
        result = "unsupported parameter";
      }
    }
    Logger.log(JSON.stringify(rowData));
    // Write new row below
    var newRange = sheet.getRange(newRow, 1, 1, rowData.length);
    newRange.setValues([rowData]);
  }
  // Return result of operation
  return ContentService.createTextOutput(result);
}
function stripQuotes( value ) {
  return value.replace(/^["']|['"]$/g, "");
}
```

##### On Arduino Level

1. Modify the Arduino code below to include your wifi credentials.
2. Insert your fingerprint, which you can get through this [link](https://www.grc.com/fingerprints.htm). 
3. Enter your web app url in the required field.
4. Replace the `GAS_ID` with your deployment id.
5. Modify the sendData function with the type of your field and any other fields you wish to record.
6. Finally, modify the string url with the names of your fields.


###### Arduino Code

```python
#include <ESP8266WiFi.h>
#include <WiFiClientSecure.h>
String readString;
const char* ssid = "Your_ssid";
const char* password = "Your_password";
const char* host = "script.google.com";
const int httpsPort = 443;
WiFiClientSecure client;
const char* fingerprint = "Your_fingerprint";
String GAS_ID = "Your_gas_id";  
void setup()
{
  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) 
  {
    delay(500);
    Serial.print(".");
  }       
}
void loop()
{
   String s=output();
   sendData(s);
}
void sendData(String x)
{
 client.setInsecure();
 Serial.print("connecting to ");
 Serial.println(host);
 if (!client.connect(host, httpsPort)) {
   Serial.println("connection failed");
   return;
 }
 if (client.verify(fingerprint, host)) {
 Serial.println("certificate matches");
 } else {
 Serial.println("certificate doesn't match");
 }
 String url = "/macros/s/" + GAS_ID + "/exec?status=" + x ;
 Serial.print("requesting URL: ");
 Serial.println(url);
 client.print(String("GET ") + url + " HTTP/1.1\r\n" +
        "Host: " + host + "\r\n" +
        "User-Agent: BuildFailureDetectorESP8266\r\n" +
        "Connection: close\r\n\r\n");
 Serial.println("request sent");
 while (client.connected()) {
 String line = client.readStringUntil('\n');
 if (line == "\r") {
   Serial.println("headers received");
   break;
 }
 }
 String line = client.readStringUntil('\n');
 if (line.startsWith("{\"state\":\"success\"")) {
 Serial.println("esp8266/Arduino CI successfull!");
 } else {
 Serial.println("esp8266/Arduino CI has failed");
 }
 Serial.println("reply was:");
 Serial.println("==========");
 Serial.println(line);
 Serial.println("==========");
 Serial.println("closing connection");
}
```
## SpreadSheet Mockups

## Flame Sensor

-	<img style="float:right; " src="../6. Google Spreadsheet/Google Spreadsheet/flamesensor.png">

## Airquality Sensor
## Temperature and Humidity Sensor
