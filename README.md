# QuarkLink ESP32 Cellular Example

This example is a simple demonstration on how to enable MQTT publish with cellular communication with DS pheripheral support for securely saving the keys.  
SIM7600G-H with ESP32-S3 is used as the hardware for this project.  
The code produces the key pairs and store them securely using DS pheripherals by the Quarklink client library.  
Then enrolment is performed to retrieve the AWS provisioning parameters.  
Finally uses this AWS credentials to publish MQTT message.  
This example is created using [ESP-MQTT](https://docs.espressif.com/projects/esp-idf/en/latest/api-reference/protocols/mqtt.html) and [esp_modem](https://github.com/espressif/esp-protocols/tree/master/components/esp_modem) APIs.

## Requirements
There are a few requirements needed in order to get started with this project:  
- **[M5Core S3](https://docs.m5stack.com/en/core/CoreS3)
- **[SIM7600G-H](https://docs.m5stack.com/en/module/comx_lte)
- **5V Power supply
- **SIM card
- **esp-idf v5.1
- **[quarklink library](https://github.com/cryptoquantique/quarklink-binaries/blob/main/quarklink-client/libquarklink-client-esp32-s3-ds-v1.4.3-debug.a)


## Setup

### Project configuration
>**Note:** It is assumed that the user has already familiarised with the difference between *virtual-efuses* and *release* during the QuarkLink provisioning process.  

Use ESP-IDF's `idf.py` command to configure the project:
- Run `idf.py set-target esp32s3` to select the target device.  

### Select quarklink-client library
To change the quarklink-client compiled library to use, simply modify the [CMakeLists.txt](./main/CMakeLists.txt) in the main folder and update with the path and name of the file needed.
E.g.
```c
add_prebuilt_library(quarklink_client "../lib/libquarklink-client-esp32-s3-ds-v1.4.3.a"
                    PRIV_REQUIRES nvs_flash esp_http_client esp_https_ota app_update mbedtls)
```
Becomes
```c
add_prebuilt_library(quarklink_client "../lib/libquarklink-client-esp32-s3-ds-v1.4.3-debug.a"
                    PRIV_REQUIRES nvs_flash esp_http_client esp_https_ota app_update mbedtls)
```

### Hardware setup
Set the DIP switch of TXDRXD as 1 & 4 - ON 2, 3, 5 and 6 -OFF.  
Turn OFF all the other DIP switches on M5.   

## Build and Execute
Modify the lines for `QUARKLINK_ENDPOINT`, `QUARKLINK_PORT` and `QUARKLINK_CERT` to have correct quarlink credential. 
Modify the APN of the SIM that you are using in the sdkconfig file `CONFIG_EXAMPLE_MODEM_PPP_APN`. Or this can be also changed in the line below of main.c.  
```esp_modem_dce_config_t dce_config = ESP_MODEM_DCE_DEFAULT_CONFIG(CONFIG_EXAMPLE_MODEM_PPP_APN);```   
Build project and flash it by clicking ESP-IDF Flash device button after selecting the correct COM port.  
Use a serial port monitor(Ex: Teraterm) to view the output messages from device.    
Register the device ID which will be printed in the serial terminal on the first time of execution to the quarklink server.  
Reboot the device again and this time the device will be enrolled to quarklink successfully and provisioning information will be retrived.  
Now the device will start publishing the data to the MQTT broker.  

## Troubleshooting
- MCU and modem UART may show UART commands syncing issue shown as below and this can be solved by power the modem first then connect the USB cable and finally reset when the blue LED in the modem board starts blinking.  
```c 
E (925) quarklink-esp32-cellular: esp_modem_get_signal_quality failed with 263 ESP_ERR_TIMEOUT
```
- Make sure the device ID is being registered after the first time power on.     
