# ESP32 Firmware Compilation

## Step 1: Prepare Your OTA Address

If you are using version 0.3.12 of this project, whether it's simple Server deployment or full module deployment, there will be an OTA address.

Since the OTA address setup methods are different for simple Server deployment and full module deployment, please choose the specific method below:

### If You're Using Simple Server Deployment

At this point, please open your OTA address in a browser. For example, my OTA address:
```
http://192.168.1.25:8003/xiaozhi/ota/
```

If it displays "OTA interface is running normally, the websocket address sent to the device is: ws://xxx:8000/xiaozhi/v1/"

You can use the project's built-in `test_page.html` to test whether you can connect to the websocket address output by the OTA page.

If you can't access it, you need to modify the `server.websocket` address in the configuration file `.config.yaml`, restart and test again until `test_page.html` can access normally.

After success, please proceed to Step 2.

### If You're Using Full Module Deployment

At this point, please open your OTA address in a browser. For example, my OTA address:
```
http://192.168.1.25:8002/xiaozhi/ota/
```

If it displays "OTA interface is running normally, websocket cluster count: X", then proceed to Step 2.

If it displays "OTA interface is not running normally", it's probably because you haven't configured the `Websocket` address in the `Control Panel`. Then:

- 1. Log in to the Control Panel as a super administrator

- 2. Click `Parameter Management` in the top menu

- 3. Find the `server.websocket` item in the list and enter your `Websocket` address. For example, mine is:

```
ws://192.168.1.25:8000/xiaozhi/v1/
```

After configuration, refresh your OTA interface address in the browser to see if it's normal. If it's still not normal, confirm again whether the Websocket is started normally and whether the Websocket address is configured.

## Step 2: Configure Environment

First, configure the project environment according to this tutorial: [Windows ESP IDF 5.3.2 Development Environment Setup and Xiaozhi Compilation](https://icnynnzcwou8.feishu.cn/wiki/JEYDwTTALi5s2zkGlFGcDiRknXf)

## Step 3: Open Configuration File

After configuring the compilation environment, download the Xiaoge xiaozhi-esp32 project source code.

Download Xiaoge's [xiaozhi-esp32 project source code](https://github.com/78/xiaozhi-esp32) from here.

After downloading, open the project in ESP-IDF, and open the `main/config.h` file.

## Step 4: Configure OTA Address

In the `main/config.h` file, find the following configuration section:

```c
// OTA Configuration
#define OTA_SERVER_URL "http://your_server_ip:port/xiaozhi/ota/"
#define WEBSOCKET_SERVER_URL "ws://your_server_ip:8000/xiaozhi/v1/"
```

Replace the placeholder values with your actual server information:

```c
// Example Configuration
#define OTA_SERVER_URL "http://192.168.1.25:8003/xiaozhi/ota/"
#define WEBSOCKET_SERVER_URL "ws://192.168.1.25:8000/xiaozhi/v1/"
```

## Step 5: Configure WiFi Settings

In the same `config.h` file, configure your WiFi settings:

```c
// WiFi Configuration
#define WIFI_SSID "your_wifi_name"
#define WIFI_PASSWORD "your_wifi_password"
```

## Step 6: Configure Device Settings

Set up device-specific parameters:

```c
// Device Configuration
#define DEVICE_NAME "Xiaozhi_ESP32"
#define DEVICE_VERSION "1.0.0"
#define WAKE_WORD "你好小智"
```

## Step 7: Compile and Flash

### Compile the Firmware

1. **Open ESP-IDF Terminal**
   - Navigate to your project directory
   - Run `idf.py build`

2. **Check for Errors**
   - Review compilation output
   - Fix any configuration errors
   - Ensure all dependencies are installed

### Flash to Device

1. **Connect ESP32 Device**
   - Connect via USB cable
   - Ensure device is recognized

2. **Flash Firmware**
   ```bash
   idf.py -p COM_PORT flash
   # Replace COM_PORT with your actual port (e.g., COM3 on Windows, /dev/ttyUSB0 on Linux)
   ```

3. **Monitor Output**
   ```bash
   idf.py -p COM_PORT monitor
   ```

## Step 8: Test the Firmware

### Basic Connectivity Test

1. **Check WiFi Connection**
   - Verify device connects to WiFi
   - Check IP address assignment

2. **Test OTA Connection**
   - Verify OTA server connectivity
   - Check websocket connection

3. **Test Voice Recognition**
   - Try wake word detection
   - Test voice command processing

### Advanced Testing

1. **Performance Testing**
   - Test response times
   - Check memory usage
   - Monitor CPU utilization

2. **Stability Testing**
   - Long-term operation test
   - Network interruption recovery
   - Power cycle testing

## Troubleshooting

### Common Compilation Issues

1. **Missing Dependencies**
   ```bash
   # Install required components
   idf.py add-dependency "espressif/esp-rainmaker"
   ```

2. **Configuration Errors**
   - Check `config.h` syntax
   - Verify all required parameters
   - Ensure proper data types

3. **Memory Issues**
   - Optimize code size
   - Enable compiler optimizations
   - Use external PSRAM if available

### Common Runtime Issues

1. **WiFi Connection Problems**
   - Check SSID and password
   - Verify network security settings
   - Test with different networks

2. **OTA Connection Issues**
   - Verify server URL format
   - Check network connectivity
   - Test with different protocols

3. **Voice Recognition Issues**
   - Check microphone functionality
   - Verify audio processing
   - Test with different wake words

## Configuration Reference

### Complete config.h Example

```c
#ifndef CONFIG_H
#define CONFIG_H

// WiFi Configuration
#define WIFI_SSID "your_wifi_name"
#define WIFI_PASSWORD "your_wifi_password"

// Server Configuration
#define OTA_SERVER_URL "http://192.168.1.25:8003/xiaozhi/ota/"
#define WEBSOCKET_SERVER_URL "ws://192.168.1.25:8000/xiaozhi/v1/"

// Device Configuration
#define DEVICE_NAME "Xiaozhi_ESP32"
#define DEVICE_VERSION "1.0.0"
#define DEVICE_ID "ESP32_001"

// Audio Configuration
#define SAMPLE_RATE 16000
#define CHANNELS 1
#define BITS_PER_SAMPLE 16

// Wake Word Configuration
#define WAKE_WORD "你好小智"
#define WAKE_WORD_TIMEOUT 5000

// Network Configuration
#define CONNECTION_TIMEOUT 10000
#define RECONNECT_INTERVAL 5000
#define MAX_RETRY_ATTEMPTS 3

// Debug Configuration
#define DEBUG_ENABLED 1
#define LOG_LEVEL 2

#endif // CONFIG_H
```

## Advanced Configuration

### Custom Wake Words

```c
// Multiple Wake Words
#define WAKE_WORDS_COUNT 3
const char* wake_words[] = {
    "你好小智",
    "小智小智",
    "嘿小智"
};
```

### Audio Processing Settings

```c
// Audio Processing Configuration
#define AUDIO_BUFFER_SIZE 1024
#define AUDIO_QUEUE_SIZE 4
#define NOISE_REDUCTION_ENABLED 1
#define ECHO_CANCELLATION_ENABLED 1
```

### Network Security

```c
// Security Configuration
#define USE_TLS 1
#define CERTIFICATE_VERIFICATION 1
#define API_KEY_REQUIRED 1
```

## Performance Optimization

### Memory Management

```c
// Memory Configuration
#define HEAP_SIZE 32768
#define STACK_SIZE 8192
#define DMA_BUFFER_SIZE 4096
```

### Power Management

```c
// Power Management
#define POWER_SAVE_MODE 1
#define SLEEP_TIMEOUT 300000  // 5 minutes
#define WAKE_ON_VOICE 1
```

## Version Control

### Firmware Versioning

```c
// Version Information
#define FIRMWARE_VERSION_MAJOR 1
#define FIRMWARE_VERSION_MINOR 0
#define FIRMWARE_VERSION_PATCH 0
#define BUILD_NUMBER 123
```

### Update Mechanism

```c
// OTA Update Configuration
#define OTA_ENABLED 1
#define OTA_CHECK_INTERVAL 3600000  // 1 hour
#define OTA_RETRY_COUNT 3
#define ROLLBACK_ENABLED 1
```

**Note: This firmware compilation guide may be outdated. Please refer to the latest ESP-IDF documentation and xiaozhi-esp32 project documentation for current compilation procedures and configuration options.**

## Additional Resources

- [ESP-IDF Programming Guide](https://docs.espressif.com/projects/esp-idf/en/latest/)
- [ESP32 Hardware Reference](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/hw-reference/)
- [Xiaozhi ESP32 Project](https://github.com/78/xiaozhi-esp32)
- [ESP-IDF Examples](https://github.com/espressif/esp-idf/tree/master/examples)
