# Telegram Application 

## Brief

Send data from NodeMCU to telegram app upon request.

## Steps



1. Download telegram app though google play or apple store if you haven’t done so already.
2. To create a telegram bot, open the app and search for `botfather`.
3. Send the message `/start` to start the process.
4. The BotFather will send you a message with many commands that you can choose from to create your bot.
5. Reply with the message “/newbot” to create a brand new bot of your own.
6. After that, the BotFather will ask you to choose a name for your bot.
7. Reply with the name of your choosing.
8. It will also ask you for a username for your bot. As specified in the message, the username should end with the word `bot`. For example: `mydevice_bot`.
9. Once you have chosen a username, your bot is successfully created.
10. Save the name, username, link, and token of your bot for later use.
11. Search for IDbot and click /start then type /getid to get your userid .
#### Method 1
1. Download the telegram bot library through the following [link](https://github.com/Gianbacchio/ESP8266-TelegramBot)
2. Open Arduino IDE, go to `Sketch`, select `Include Library` and click on `Add .ZIP Library..`, and choose the telegram library file.
##### In the code below:
1. Fill in your ssid and password.
2. Fill in the BOTtoken with the token of your bot.
3. Fill in the BOTname with the name of your bot.
4. Fill in the BOTusername with the username of your bot.
5. The code below is made to notify the user in case of a fire, but it only runs by request, i.e. it will only notify you when you request it to.
6. To do so, after compiling your code to your device, open the telegram app, and enter the link of the bot you created.
7. Finally, send the message `/start`.

////Nariman: ADD CODE


#### Method 2

1. For the Arduino IDE install the universaltelegram library from this site  Universal Telegram Bot Library and add it to your code by clicking on include library > add zip file 
2. Include arduinoJson version 6.15.2

```python
#include <ESP8266WiFi.h>
#include <WiFiClientSecure.h>
#include <UniversalTelegramBot.h> 
#include <ArduinoJson.h>

const char* ssid = "your_ssid";
const char* password = "your_pass";

#define CHAT_ID "your_chat_id"

#define BOTtoken "your_bot_token"  

WiFiClientSecure client;
UniversalTelegramBot bot(BOTtoken, client);

//Handle what happens when you receive new messages
void handleNewMessages(int numNewMessages) {
  Serial.println("handleNewMessages");
  Serial.println(String(numNewMessages));

  for (int i=0; i<numNewMessages; i++) {
    // Chat id of the requester
    String chat_id = String(bot.messages[i].chat_id);
    if (chat_id != CHAT_ID){
      bot.sendMessage(chat_id, "Unauthorized user", "");
      continue;
    }
    
    String text = bot.messages[i].text;
    Serial.println(text);

    String from_name = bot.messages[i].from_name;

    if (text == "/start") {
      String welcome = "Welcome, " + from_name + ".\n";
      welcome += "Use the following command to get current readings.\n\n";
      welcome += "/readings \n";
      bot.sendMessage(chat_id, welcome, "");
    }

    if (text == "/readings") {
      String readings = output();
      bot.sendMessage(chat_id, readings, "");
    }  
  }
}

void setup() {
  Serial.begin(115200);
  client.setInsecure();

  // Connect to Wi-Fi
  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to WiFi..");
  }
  Serial.println(WiFi.localIP());
}

void loop() {
    int numNewMessages = bot.getUpdates(bot.last_message_received + 1);
       
    while(numNewMessages) {
      Serial.println("got response");
      handleNewMessages(numNewMessages);
      numNewMessages = bot.getUpdates(bot.last_message_received + 1);
  }
}
```
## Mockups

### Method 1

<img style="float:right; " src="../5. Telegram Application/Telegram Application/method1.jpeg">

### Method 2
