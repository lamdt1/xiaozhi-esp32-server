# Weather Plugin Usage Guide

## Overview

The weather plugin `get_weather` is one of the core functions of the Xiaozhi ESP32 voice assistant, supporting voice queries for weather information across the country. The plugin is based on the Hefeng Weather API, providing real-time weather and 7-day weather forecast functionality.

## API Key Application Guide

### 1. Register Hefeng Weather Account

1. Visit [Hefeng Weather Console](https://console.qweather.com/)
2. Register an account and complete email verification
3. Log in to the console

### 2. Create Application and Get API Key

1. After entering the console, click ["Project Management"](https://console.qweather.com/project?lang=zh) on the right → "Create Project"
2. Fill in project information:
   - **Project Name**: e.g., "Xiaozhi Voice Assistant"
3. Click Save
4. After project creation is complete, click "Create Credentials" in that project
5. Fill in credential information:
   - **Credential Name**: e.g., "Xiaozhi Voice Assistant"
   - **Authentication Method**: Select "API Key"
6. Click Save
7. Copy the `API Key` from the credentials - this is the first key configuration information

### 3. Get API Host

1. In the console, click ["Settings"](https://console.qweather.com/setting?lang=zh) → "API Host"
2. Copy the API Host address - this is the second key configuration information

## Configuration Steps

### 1. Configure API Parameters

Edit the `config.yaml` file and add the following configuration:

```yaml
plugins:
  get_weather:
    api_host: "your_api_host_from_step_3"
    api_key: "your_api_key_from_step_2"
    default_location: "Beijing"  # Default city, can be modified
```

### 2. Enable Weather Plugin

In the `config.yaml` file, ensure the weather plugin is enabled:

```yaml
selected_module:
  Intent: function_call  # Enable function call intent

Intent:
  function_call:
    type: function_call
    functions:
      - get_weather  # Add weather function
```

### 3. Test Configuration

1. Restart the xiaozhi-server service
2. Test with voice commands like "What's the weather like today?"
3. Verify weather information is returned correctly

## Usage Examples

### Basic Weather Queries

- **Current Weather**: "What's the weather like today?"
- **Specific City**: "What's the weather like in Shanghai?"
- **Temperature**: "What's the temperature?"
- **Weather Conditions**: "Is it sunny today?"

### Advanced Queries

- **Multi-day Forecast**: "What's the weather like for the next few days?"
- **Specific Date**: "What's the weather like tomorrow?"
- **Weather Alerts**: "Are there any weather warnings?"

## Configuration Parameters

### Required Parameters

| Parameter | Description | Example |
|-----------|-------------|---------|
| `api_host` | Hefeng Weather API host | `mj7p3y7naa.re.qweatherapi.com` |
| `api_key` | API authentication key | `a861d0d5e7bf4ee1a83d9a9e4f96d4da` |
| `default_location` | Default query city | `Beijing` |

### Optional Parameters

| Parameter | Description | Default | Example |
|-----------|-------------|---------|---------|
| `language` | Response language | `zh` | `en`, `zh` |
| `unit` | Temperature unit | `metric` | `metric`, `imperial` |
| `forecast_days` | Forecast days | `7` | `3`, `7`, `15` |

### Advanced Configuration

```yaml
plugins:
  get_weather:
    # Basic Configuration
    api_host: "mj7p3y7naa.re.qweatherapi.com"
    api_key: "a861d0d5e7bf4ee1a83d9a9e4f96d4da"
    default_location: "Beijing"
    
    # Advanced Configuration
    language: "zh"
    unit: "metric"
    forecast_days: 7
    
    # Cache Configuration
    cache_duration: 300  # Cache for 5 minutes
    max_cache_size: 100  # Maximum cache entries
    
    # Error Handling
    retry_attempts: 3
    timeout: 10
    fallback_location: "Beijing"
```

## API Usage and Limits

### Free Tier Limits

- **Daily Requests**: 1,000 calls per day
- **Concurrent Requests**: 10 requests per second
- **Data Retention**: 7 days of historical data

### Paid Tier Benefits

- **Higher Limits**: Up to 100,000 calls per day
- **Extended Forecast**: Up to 15 days
- **Historical Data**: Up to 30 days
- **Priority Support**: Faster response times

### Rate Limiting

```yaml
# Rate Limiting Configuration
rate_limiting:
  enabled: true
  requests_per_minute: 60
  burst_limit: 10
  cooldown_period: 60
```

## Error Handling

### Common Error Codes

| Error Code | Description | Solution |
|------------|-------------|----------|
| `401` | Invalid API key | Check API key configuration |
| `403` | Access denied | Verify API permissions |
| `429` | Rate limit exceeded | Implement rate limiting |
| `500` | Server error | Retry after delay |

### Error Handling Configuration

```yaml
# Error Handling Settings
error_handling:
  retry_attempts: 3
  retry_delay: 1
  fallback_response: "Weather service temporarily unavailable"
  log_errors: true
  alert_threshold: 10
```

## Performance Optimization

### Caching Strategy

```yaml
# Cache Configuration
cache:
  enabled: true
  duration: 300  # 5 minutes
  max_size: 100
  cleanup_interval: 3600  # 1 hour
  compression: true
```

### Response Optimization

```yaml
# Response Optimization
optimization:
  compress_responses: true
  minify_json: true
  cache_headers: true
  gzip_compression: true
```

## Monitoring and Logging

### Log Configuration

```yaml
# Logging Settings
logging:
  level: INFO
  format: "%(asctime)s - %(name)s - %(levelname)s - %(message)s"
  file: "logs/weather.log"
  max_size: "10MB"
  backup_count: 5
```

### Monitoring Metrics

```yaml
# Monitoring Configuration
monitoring:
  enabled: true
  metrics:
    - request_count
    - response_time
    - error_rate
    - cache_hit_rate
  alerts:
    - high_error_rate
    - slow_response_time
    - api_quota_exceeded
```

## Security Considerations

### API Key Security

1. **Secure Storage**: Store API keys in environment variables
2. **Access Control**: Limit API key permissions
3. **Rotation**: Regularly rotate API keys
4. **Monitoring**: Monitor API key usage

### Network Security

```yaml
# Security Configuration
security:
  https_only: true
  api_key_encryption: true
  request_validation: true
  rate_limiting: true
  ip_whitelist: ["192.168.1.0/24"]
```

## Troubleshooting

### Common Issues

1. **API Key Invalid**
   - Check API key format
   - Verify key permissions
   - Test key with API directly

2. **Network Connectivity**
   - Test network connection
   - Check firewall settings
   - Verify DNS resolution

3. **Response Parsing**
   - Check API response format
   - Verify JSON structure
   - Test with sample data

### Debug Mode

```yaml
# Debug Configuration
debug:
  enabled: true
  log_level: DEBUG
  verbose_logging: true
  api_response_logging: true
  performance_monitoring: true
```

## Integration Examples

### Voice Commands

```python
# Example voice command processing
def process_weather_command(text):
    if "天气" in text or "weather" in text.lower():
        location = extract_location(text)
        weather_data = get_weather_data(location)
        return format_weather_response(weather_data)
    return None
```

### Custom Responses

```python
# Custom weather response formatting
def format_weather_response(data):
    return f"""
    今天{data['location']}的天气是{data['condition']}，
    温度{data['temperature']}度，
    湿度{data['humidity']}%，
    风力{data['wind']}级。
    """
```

## Future Enhancements

### Planned Features

1. **Multi-language Support**
   - Support for multiple languages
   - Localized weather descriptions
   - Cultural weather references

2. **Advanced Forecasting**
   - Hourly forecasts
   - Weather alerts and warnings
   - Seasonal weather patterns

3. **Integration Expansion**
   - Calendar integration
   - Travel planning
   - Agricultural weather data

**Note: This weather integration guide may be outdated. Please refer to the latest Hefeng Weather API documentation and xiaozhi-esp32-server documentation for current configuration options and API changes.**
