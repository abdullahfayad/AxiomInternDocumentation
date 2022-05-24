# Email Service

## Brief

Send an email (choose sender and receiver of your choice) upon NodeMCU startup.

## Steps

1. Create an account on the smtp2go [website](www.smtp2go.com)
2. Press on Try SMTP2GO Free.
3. Fill in the required fields as required.
4. Save your username and password since you will be using them to send the email.
5. Encode your username and password to Base64 format using any website. 
For example this [link](https://www.base64encode.org)
6. Set the destination character set to be ASCII.
7. Copy and save the encoded username and password for later use.


#### On Arduino Level

1. Define the variable server which will include the smtp server

```python 
char server[] = "mail.smtp2go.com";   
```

2. Modify the following variables to include the email you’re sending from and the receiving email

```python
String sender_email= "nariman.rifaii@gmail.com";
String receiver_email="nrifai@axiomworks.ai";
```

3. Include your encoded username and password

```python 
String encoded_username = " ";
String encoded_password= " ";
```

4. Create a new instance of WiFiClient
```python 
WiFiClient espClient;
```
## Arduino Code

```python
#include <ESP8266WiFi.h>

const char* ssid = "Your_ssid";   // Enter the namme of your WiFi Network.
const char* password = "Your_password";  // Enter the Password of your WiFi Network.

char server[] = "mail.smtp2go.com";   // The SMTP Server
String encoded_username="your_encoded_username"; 
String encoded_password="your_encoded_password"; 
String sender_email= "sender_Email";
String receiver_email="receiver_email";


WiFiClient espClient;

void setup(){
  Serial.begin(115200);
  delay(10);
  Serial.println("");
  Serial.println("");
  Serial.print("Connecting To: ");
  Serial.println(ssid);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED){
    delay(500);
    Serial.print("*");
  }
  Serial.println("");
  Serial.println("WiFi Connected.");
  Serial.print("IP address: ");
  Serial.println(WiFi.localIP());
  byte ret = sendEmail();
}

void loop(){
  String d=output();
  Serial.println(d);
}

byte sendEmail(){
  if (espClient.connect(server, 2525) == 1){
    	Serial.println("Connected to espClient");
  }
  			else{
    				Serial.println("connection failed");
    				return 0;
  			}
  			if (!emailResp())
    				return 0;
  			String stamp= gettime();
  			Serial.println("Sending EHLO:");
  			espClient.println("EHLO www.example.com");
  			if (!emailResp())
    				return 0;
  			Serial.println("Sending auth login");
  			espClient.println("AUTH LOGIN");
  			if (!emailResp())
    				return 0;
  			Serial.println("Sending Username:");
  			espClient.println(encoded_username); 
  			if (!emailResp())
    				return 0;
  			Serial.println("Sending Password:");
  			espClient.println(encoded_password);
  			if (!emailResp())
    				return 0;
  			Serial.println("Sending From");
  			String line1 = "MAIL From: "+sender_email;
  			espClient.println(line1);
  			if (!emailResp())
    				return 0;
  			Serial.println("Sending To");
  			String line2 = "RCPT To: "+ receiver_email;
  			espClient.println(line2);
  			if (!emailResp())
    				return 0;
  			Serial.println("Sending DATA");
  			espClient.println("DATA");
  			if (!emailResp())
    				return 0;
  			Serial.println("Sending email");
  			String line3 = "To:  " + receiver_email;
  			espClient.println(line3);
  			String line4= "From: "+sender_email;
  			espClient.println(line4); // Enter Sender Mail Id
espClient.println("Subject: Nariman's ESP8266 e-mail\r\n");
espClient.println("This is an e-mail sent from Nariman's ESP8266.\n");
  			// send email on startup
                 espClient.println("We would like to inform you that your ESP8266 has been started.");
  			}
  			String line = "Current Reading: "+output();
  			espClient.println(line);
  			espClient.println(".");
  			if (!emailResp())
    				return 0;
  			Serial.println("Sending QUIT");
  			espClient.println("QUIT");
  			if (!emailResp())
    				return 0;
  			espClient.stop();
  			Serial.println("disconnected");
  			return 1;
}

byte emailResp(){
  byte responseCode;
  byte readByte;
  int loopCount = 0;
  while (!espClient.available()){
    delay(1);
    loopCount++;
    if (loopCount > 20000){
      espClient.stop();
      Serial.println(F("\r\nTimeout"));
      return 0;
    }
  }
  responseCode = espClient.peek();
  while (espClient.available()){
    readByte = espClient.read();
    Serial.write(readByte);
  }
  if (responseCode >= '4'){
    return 0;
  }
  return 1;
}
```
## Email Mockups

<img style="float:right; " src="../4. Email Service/Email/flameemail.png">
