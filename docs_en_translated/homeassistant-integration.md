# Xiaozhi ESP32 Open Source Server Integration Guide with Home Assistant

[TOC]

-----

## Introduction

This document will guide you on how to integrate ESP32 devices with Home Assistant.

## Prerequisites

- Home Assistant is installed and configured
- For this integration, I chose the model: free ChatGLM, which supports function call capabilities

## Pre-Integration Setup (Required)

### 1. Get HA Network Address Information

Please visit your Home Assistant network address. For example, if my HA address is 192.168.4.7 and the port is the default 8123, open in browser:

```
http://192.168.4.7:8123
```

> **Manual HA IP Address Query Method** (only applicable when xiaozhi-esp32-server and HA are deployed on the same network device [e.g., same WiFi]):
>
> 1. Enter Home Assistant (frontend).
>
> 2. Click the bottom left **Settings** → **System** → **Network**.
>
> 3. Scroll to the bottom `Home Assistant URL` area, in `local network`, click the `eye` button to see the currently used IP address (like `192.168.1.10`) and network interface. Click `copy link` to copy directly.
>
>    ![image-20250504051716417](images/image-ha-integration-01.png)

Or, if you have already set up a directly accessible Home Assistant OAuth address, you can also access it directly in the browser:

```
http://homeassistant.local:8123
```

### 2. Login to Home Assistant to Get Development Key

Log in to `Home Assistant`, click `bottom left avatar -> profile`, switch to `Security` navigation bar, scroll to the bottom `Long-term access tokens` to generate api_key, and copy and save it. All subsequent methods need to use this api key and it only appears once (small tip: You can save the generated QR code image, and scan the QR code later to extract the api key again).

## Method 1: HA Call Function Built by Xiaozhi Community

### Function Description

- If you need to add new devices later, this method requires manually restarting the `xiaozhi-esp32-server` to update device information (**Important**).

### Configuration Steps

1. **Configure Home Assistant Connection**
   - Set `home_assistant.base_url` to your HA address (e.g., `http://192.168.4.7:8123`)
   - Set `home_assistant.api_key` to the long-term access token obtained above

2. **Configure Device Entities**
   - Add your device entities in the `home_assistant.devices` section
   - Format: `room,device_name,entity_id`
   - Example: `客厅,玩具灯,switch.cuco_cn_460494544_cp1_on_p_2_1`

3. **Test the Integration**
   - Restart the xiaozhi-server
   - Test voice commands like "turn on the living room toy light"
   - Verify device control functionality

### Device Configuration Format

```yaml
home_assistant:
  devices:
    - 客厅,玩具灯,switch.cuco_cn_460494544_cp1_on_p_2_1
    - 卧室,台灯,switch.iot_cn_831898993_socn1_on_p_2_1
  base_url: http://homeassistant.local:8123
  api_key: your_home_assistant_api_access_token
```

## Method 2: Direct Home Assistant Integration

### Function Description

- This method provides direct integration with Home Assistant
- Supports real-time device status updates
- Enables advanced automation scenarios

### Configuration Steps

1. **Enable Home Assistant Integration**
   - Set up Home Assistant integration in the xiaozhi-server configuration
   - Configure authentication and connection parameters

2. **Device Discovery**
   - Enable automatic device discovery
   - Configure device filtering and categorization
   - Set up device naming conventions

3. **Voice Control Setup**
   - Configure wake words for device control
   - Set up command patterns for different devices
   - Test voice recognition accuracy

### Advanced Configuration

```yaml
# Home Assistant Integration Settings
home_assistant:
  # Basic Connection
  base_url: "http://homeassistant.local:8123"
  api_key: "your_long_term_access_token"
  
  # Device Configuration
  devices:
    - name: "Living Room Light"
      entity_id: "light.living_room"
      room: "客厅"
      type: "light"
    - name: "Bedroom Fan"
      entity_id: "fan.bedroom"
      room: "卧室"
      type: "fan"
  
  # Advanced Settings
  auto_discovery: true
  device_filter:
    include_domains: ["light", "switch", "fan", "climate"]
    exclude_entities: ["switch.unwanted_switch"]
  
  # Voice Control
  voice_control:
    wake_words: ["小智", "小爱"]
    command_patterns:
      turn_on: ["打开", "开启", "启动"]
      turn_off: ["关闭", "关掉", "停止"]
      brightness: ["亮度", "调亮", "调暗"]
```

## Method 3: Custom Integration

### Function Description

- For advanced users who need custom integration logic
- Supports custom device types and control methods
- Enables integration with non-standard Home Assistant setups

### Implementation Steps

1. **Create Custom Integration Module**
   ```python
   # Custom Home Assistant Integration
   class CustomHAIntegration:
       def __init__(self, config):
           self.config = config
           self.ha_client = HomeAssistantClient(config)
       
       def control_device(self, device_name, action, **kwargs):
           # Custom device control logic
           pass
   ```

2. **Configure Custom Integration**
   ```yaml
   custom_integrations:
     home_assistant:
       type: custom
       module_path: "plugins/custom_ha_integration.py"
       config:
         base_url: "http://homeassistant.local:8123"
         api_key: "your_api_key"
   ```

## Testing and Verification

### Basic Testing

1. **Connection Test**
   - Verify Home Assistant connectivity
   - Test API authentication
   - Check device discovery

2. **Voice Control Test**
   - Test basic device control commands
   - Verify command recognition accuracy
   - Test error handling and feedback

3. **Device Status Test**
   - Verify device status updates
   - Test real-time status monitoring
   - Check status synchronization

### Advanced Testing

1. **Automation Testing**
   - Test complex automation scenarios
   - Verify trigger conditions
   - Check automation execution

2. **Performance Testing**
   - Test response times
   - Verify concurrent device control
   - Check system resource usage

## Troubleshooting

### Common Issues

1. **Connection Issues**
   - Check network connectivity
   - Verify Home Assistant URL and port
   - Test API key validity

2. **Device Control Issues**
   - Verify entity IDs are correct
   - Check device permissions
   - Test device functionality in Home Assistant

3. **Voice Recognition Issues**
   - Check microphone quality
   - Verify wake word detection
   - Test command recognition accuracy

### Error Messages and Solutions

| Error Message | Possible Cause | Solution |
|---------------|----------------|----------|
| "Connection failed" | Network or URL issue | Check network and URL configuration |
| "Authentication failed" | Invalid API key | Verify and update API key |
| "Device not found" | Incorrect entity ID | Check entity ID in Home Assistant |
| "Permission denied" | Insufficient permissions | Check API key permissions |

## Best Practices

### Security

1. **API Key Management**
   - Use long-term access tokens
   - Rotate keys regularly
   - Store keys securely

2. **Network Security**
   - Use HTTPS when possible
   - Implement proper firewall rules
   - Monitor access logs

### Performance

1. **Device Management**
   - Limit device discovery scope
   - Use efficient polling intervals
   - Implement caching where appropriate

2. **Voice Processing**
   - Optimize wake word detection
   - Use efficient command processing
   - Implement proper error handling

### Maintenance

1. **Regular Updates**
   - Keep Home Assistant updated
   - Update integration components
   - Monitor for compatibility issues

2. **Monitoring**
   - Set up system monitoring
   - Monitor integration performance
   - Track error rates and patterns

## Advanced Features

### Scene Control

```yaml
# Scene Configuration
scenes:
  - name: "Movie Night"
    entities:
      - light.living_room: "dim"
      - switch.tv: "on"
      - fan.living_room: "off"
  
  - name: "Sleep Mode"
    entities:
      - light.bedroom: "off"
      - switch.bedroom_fan: "on"
      - climate.bedroom: "cool"
```

### Automation Integration

```yaml
# Automation Rules
automations:
  - trigger: "voice_command"
    condition: "time_after_sunset"
    action: "scene.movie_night"
  
  - trigger: "voice_command"
    condition: "time_after_22:00"
    action: "scene.sleep_mode"
```

## Future Enhancements

### Planned Features

1. **Advanced Voice Control**
   - Natural language processing
   - Context-aware commands
   - Multi-language support

2. **Smart Automation**
   - Learning user preferences
   - Predictive device control
   - Advanced scheduling

3. **Integration Expansion**
   - Support for more device types
   - Third-party service integration
   - Cloud service connectivity

**Note: This integration guide may be outdated. Please refer to the latest Home Assistant documentation and xiaozhi-esp32-server documentation for current integration methods and configuration options.**
