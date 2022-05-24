# Azure IoT Hub

## Brief

Route sensor data to Azure IoT Hub.

## Steps

1. Create an IoT Hub and a device.
2. Install IoT Hub library through the following [website](https://github.com/Azure/azure-iot-arduino).
3. In Arduino IDE, go to Sketch > Include Library > Add ZIP library, and choose the downloaded file.
4. Restart your Arduino IDE, then go to File > Examples > Azure Iot Hub > esp8266 > iothub_ll_telemetry_sample.
5. Modify the following in the `iot_configs.h` file:
   1. Your WiFi Credentials:
      ```python
      #define IOT_CONFIG_WIFI_SSID            "Your_ssid"
      #define IOT_CONFIG_WIFI_PASSWORD        "Your_password"
      ```
   2. Your Connection String:
      ```python
      #define DEVICE_CONNECTION_STRING    "Your_connection_string”
      ```
      <img style="float:right; " src="../../../Images/problem.png" width=50> Note: You can get this device connection string from the properties of the IoT device (on Azure Website).
6. Modify the `run_demo` function as follows:
  ```python
  static void run_demo()
  {
    int result = 0;
    do
    {
        delay(1000);
        String out = "{Message_Number:"+String(messages_sent)+" ,Status:\"";
        String s = output();
        out = out + s + "\"}";
        telemetry_msg = out.c_str();
        Serial.println(telemetry_msg);
        message_handle = IoTHubMessage_CreateFromString(telemetry_msg);
        LogInfo("Sending message %d to IoTHub\r\n", (int)(messages_sent + 1));
        result = IoTHubDeviceClient_LL_SendEventAsync(device_ll_handle, message_handle, send_confirm_callback, NULL);
        IoTHubMessage_Destroy(message_handle);
        messages_sent++;
        IoTHubDeviceClient_LL_DoWork(device_ll_handle);
        ThreadAPI_Sleep(3);
        reset_esp_helper();
    } while (g_continueRunning);
    IoTHubDeviceClient_LL_Destroy(device_ll_handle);
    IoTHub_Deinit();
    LogInfo("done with sending");
    return;
  }
  ```
7. Finally, add the [Output](../../Part%20I:%20Collecting%20Data/Collecting%20Data.md) function to your code and compile to your NodeMCU.
