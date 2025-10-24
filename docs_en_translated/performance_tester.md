# Performance Testing Tool Usage Guide for Speech Recognition, Large Language Models, Non-Streaming Speech Synthesis, Streaming Speech Synthesis, and Vision Models

## Overview

This performance testing tool is designed to evaluate the performance of various AI components in the xiaozhi-esp32-server system, including:

- **Speech Recognition (ASR)**: Voice-to-text conversion performance
- **Large Language Models (LLM)**: Text generation and understanding performance
- **Non-Streaming Speech Synthesis (TTS)**: Text-to-speech conversion performance
- **Streaming Speech Synthesis**: Real-time voice generation performance
- **Vision Models (VLLM)**: Image analysis and understanding performance

## Setup Instructions

### 1. Create Required Directories

```bash
# Navigate to the main xiaozhi-server directory
cd main/xiaozhi-server

# Create data directory
mkdir -p data

# Create logs directory
mkdir -p logs
```

### 2. Create Configuration File

Create a `.config.yaml` file in the `data` directory with your AI service parameters:

```yaml
# Example configuration for performance testing
LLM:
  ChatGLMLLM:
    # Define LLM API type
    type: openai
    # glm-4-flash is free but still requires API key registration
    # You can find your API key here: https://bigmodel.cn/usercenter/proj-mgmt/apikeys
    model_name: glm-4-flash
    url: https://open.bigmodel.cn/api/paas/v4/
    api_key: your_chat_glm_web_key

TTS:
  EdgeTTS:
    type: edge
    voice: zh-CN-XiaoxiaoNeural
    output_dir: tmp/

VLLM:
  ChatGLMVLLM:
    type: openai
    model_name: glm-4v-flash
    url: https://open.bigmodel.cn/api/paas/v4/
    api_key: your_api_key

ASR:
  FunASR:
    type: fun_local
    model_dir: models/SenseVoiceSmall
    output_dir: tmp/
```

### 3. Install Dependencies

Ensure all required dependencies are installed:

```bash
# Install Python dependencies
pip install -r requirements.txt

# Install additional testing dependencies
pip install pytest pytest-benchmark memory-profiler
```

## Running Performance Tests

### Basic Performance Test

```bash
# Run the performance tester
python performance_tester.py
```

### Advanced Testing Options

```bash
# Run with specific test parameters
python performance_tester.py --test-type asr --iterations 100

# Run with custom configuration
python performance_tester.py --config data/.config.yaml

# Run with verbose output
python performance_tester.py --verbose

# Run specific component tests
python performance_tester.py --components llm,tts
```

## Test Configuration

### ASR (Speech Recognition) Testing

```yaml
# ASR Test Configuration
ASR:
  test_files:
    - "test_audio/sample1.wav"
    - "test_audio/sample2.wav"
    - "test_audio/sample3.wav"
  
  test_parameters:
    sample_rate: 16000
    channels: 1
    duration: 10  # seconds
    
  performance_metrics:
    - accuracy
    - response_time
    - memory_usage
    - cpu_usage
```

### LLM (Large Language Model) Testing

```yaml
# LLM Test Configuration
LLM:
  test_prompts:
    - "你好，请介绍一下你自己"
    - "What's the weather like today?"
    - "请用100字概括量子计算的基本原理和应用前景"
  
  test_parameters:
    max_tokens: 500
    temperature: 0.7
    top_p: 1.0
    
  performance_metrics:
    - response_time
    - token_generation_rate
    - memory_usage
    - api_cost
```

### TTS (Text-to-Speech) Testing

```yaml
# TTS Test Configuration
TTS:
  test_texts:
    - "你好，我是小智，很高兴为您服务"
    - "今天天气很好，适合出门散步"
    - "人工智能技术正在快速发展"
  
  test_parameters:
    voice: "zh-CN-XiaoxiaoNeural"
    speed: 1.0
    pitch: 1.0
    
  performance_metrics:
    - generation_time
    - audio_quality
    - memory_usage
    - file_size
```

### VLLM (Vision Language Model) Testing

```yaml
# VLLM Test Configuration
VLLM:
  test_images:
    - "test_images/sample1.jpg"
    - "test_images/sample2.png"
    - "test_images/sample3.jpeg"
  
  test_parameters:
    max_tokens: 1000
    temperature: 0.7
    
  performance_metrics:
    - response_time
    - accuracy
    - memory_usage
    - gpu_usage
```

## Performance Metrics

### Response Time Metrics

```python
# Response time measurement
import time

def measure_response_time(func, *args, **kwargs):
    start_time = time.time()
    result = func(*args, **kwargs)
    end_time = time.time()
    response_time = end_time - start_time
    return result, response_time
```

### Memory Usage Metrics

```python
# Memory usage measurement
import psutil
import os

def measure_memory_usage():
    process = psutil.Process(os.getpid())
    memory_info = process.memory_info()
    return {
        'rss': memory_info.rss,  # Resident Set Size
        'vms': memory_info.vms,  # Virtual Memory Size
        'percent': process.memory_percent()
    }
```

### CPU Usage Metrics

```python
# CPU usage measurement
import psutil

def measure_cpu_usage():
    return {
        'cpu_percent': psutil.cpu_percent(interval=1),
        'cpu_count': psutil.cpu_count(),
        'load_average': psutil.getloadavg()
    }
```

## Test Results Analysis

### Performance Report Generation

```python
# Generate performance report
def generate_performance_report(test_results):
    report = {
        'summary': {
            'total_tests': len(test_results),
            'passed_tests': sum(1 for r in test_results if r['status'] == 'passed'),
            'failed_tests': sum(1 for r in test_results if r['status'] == 'failed'),
            'average_response_time': calculate_average_response_time(test_results)
        },
        'detailed_results': test_results,
        'recommendations': generate_recommendations(test_results)
    }
    return report
```

### Benchmarking

```python
# Benchmarking configuration
BENCHMARK_CONFIG = {
    'iterations': 100,
    'warmup_iterations': 10,
    'timeout': 300,  # 5 minutes
    'memory_limit': '2GB',
    'cpu_limit': '80%'
}
```

## Advanced Testing Features

### Load Testing

```bash
# Run load tests
python performance_tester.py --load-test --concurrent-users 10 --duration 300
```

### Stress Testing

```bash
# Run stress tests
python performance_tester.py --stress-test --max-load 100
```

### Endurance Testing

```bash
# Run endurance tests
python performance_tester.py --endurance-test --duration 3600  # 1 hour
```

## Custom Test Scenarios

### Scenario 1: Real-time Voice Processing

```python
# Real-time voice processing test
def test_realtime_voice_processing():
    # Simulate continuous voice input
    for i in range(100):
        audio_data = generate_test_audio(duration=5)
        start_time = time.time()
        
        # Process audio
        result = asr.process(audio_data)
        response = llm.generate_response(result)
        audio_output = tts.synthesize(response)
        
        end_time = time.time()
        processing_time = end_time - start_time
        
        # Verify real-time performance
        assert processing_time < 2.0  # Should process within 2 seconds
```

### Scenario 2: Multi-modal Processing

```python
# Multi-modal processing test
def test_multimodal_processing():
    # Test image + text processing
    image_data = load_test_image()
    text_prompt = "Describe this image"
    
    start_time = time.time()
    result = vllm.process_image_and_text(image_data, text_prompt)
    end_time = time.time()
    
    processing_time = end_time - start_time
    assert processing_time < 5.0  # Should process within 5 seconds
```

### Scenario 3: Concurrent Processing

```python
# Concurrent processing test
import concurrent.futures

def test_concurrent_processing():
    with concurrent.futures.ThreadPoolExecutor(max_workers=5) as executor:
        futures = []
        
        # Submit multiple tasks
        for i in range(10):
            future = executor.submit(process_voice_command, f"test_command_{i}")
            futures.append(future)
        
        # Wait for all tasks to complete
        results = [future.result() for future in futures]
        
        # Verify all tasks completed successfully
        assert all(result['status'] == 'success' for result in results)
```

## Performance Optimization

### Optimization Recommendations

```python
# Performance optimization recommendations
OPTIMIZATION_RECOMMENDATIONS = {
    'asr': {
        'model_optimization': 'Use quantized models for faster inference',
        'batch_processing': 'Process multiple audio samples in batches',
        'caching': 'Cache frequently used models in memory'
    },
    'llm': {
        'model_selection': 'Use smaller models for faster response times',
        'prompt_optimization': 'Optimize prompts for better performance',
        'streaming': 'Use streaming responses for real-time interaction'
    },
    'tts': {
        'voice_caching': 'Cache generated audio for repeated phrases',
        'batch_synthesis': 'Synthesize multiple texts in batches',
        'audio_compression': 'Use efficient audio compression'
    }
}
```

### Configuration Tuning

```yaml
# Performance tuning configuration
performance_tuning:
  asr:
    batch_size: 4
    max_audio_length: 30
    enable_caching: true
    
  llm:
    max_tokens: 500
    temperature: 0.7
    enable_streaming: true
    
  tts:
    voice_cache_size: 100
    audio_quality: "high"
    enable_compression: true
```

## Monitoring and Alerting

### Real-time Monitoring

```python
# Real-time performance monitoring
class PerformanceMonitor:
    def __init__(self):
        self.metrics = {}
        self.alerts = []
    
    def monitor_performance(self, component, metrics):
        # Track performance metrics
        self.metrics[component] = metrics
        
        # Check for performance issues
        if metrics['response_time'] > 5.0:
            self.alerts.append(f"{component} response time too high: {metrics['response_time']}s")
        
        if metrics['memory_usage'] > 80:
            self.alerts.append(f"{component} memory usage too high: {metrics['memory_usage']}%")
```

### Performance Alerts

```python
# Performance alert configuration
ALERT_THRESHOLDS = {
    'response_time': 5.0,  # seconds
    'memory_usage': 80,    # percentage
    'cpu_usage': 90,       # percentage
    'error_rate': 5.0      # percentage
}
```

## Test Data Management

### Test Data Generation

```python
# Generate test data
def generate_test_data():
    # Generate test audio files
    generate_test_audio_files(count=100, duration=10)
    
    # Generate test images
    generate_test_images(count=50, resolution=(512, 512))
    
    # Generate test texts
    generate_test_texts(count=200, length_range=(10, 100))
```

### Test Data Validation

```python
# Validate test data
def validate_test_data():
    # Validate audio files
    for audio_file in test_audio_files:
        assert validate_audio_format(audio_file)
        assert validate_audio_quality(audio_file)
    
    # Validate image files
    for image_file in test_image_files:
        assert validate_image_format(image_file)
        assert validate_image_quality(image_file)
```

## Reporting and Visualization

### Performance Report Generation

```python
# Generate comprehensive performance report
def generate_performance_report():
    report = {
        'test_summary': generate_test_summary(),
        'performance_metrics': calculate_performance_metrics(),
        'recommendations': generate_optimization_recommendations(),
        'charts': generate_performance_charts()
    }
    
    # Save report
    save_report(report, 'performance_report.json')
    generate_html_report(report, 'performance_report.html')
```

### Performance Visualization

```python
# Performance visualization
import matplotlib.pyplot as plt

def visualize_performance(metrics):
    # Response time chart
    plt.figure(figsize=(12, 8))
    plt.subplot(2, 2, 1)
    plt.plot(metrics['response_times'])
    plt.title('Response Time Over Time')
    plt.xlabel('Test Iteration')
    plt.ylabel('Response Time (s)')
    
    # Memory usage chart
    plt.subplot(2, 2, 2)
    plt.plot(metrics['memory_usage'])
    plt.title('Memory Usage Over Time')
    plt.xlabel('Test Iteration')
    plt.ylabel('Memory Usage (%)')
    
    # CPU usage chart
    plt.subplot(2, 2, 3)
    plt.plot(metrics['cpu_usage'])
    plt.title('CPU Usage Over Time')
    plt.xlabel('Test Iteration')
    plt.ylabel('CPU Usage (%)')
    
    # Error rate chart
    plt.subplot(2, 2, 4)
    plt.plot(metrics['error_rates'])
    plt.title('Error Rate Over Time')
    plt.xlabel('Test Iteration')
    plt.ylabel('Error Rate (%)')
    
    plt.tight_layout()
    plt.savefig('performance_analysis.png')
    plt.show()
```

**Note: This performance testing guide may be outdated. Please refer to the latest project documentation for current testing procedures and configuration options.**

## Additional Resources

- [Performance Testing Best Practices](https://docs.pytest.org/en/latest/performance.html)
- [Python Profiling Tools](https://docs.python.org/3/library/profile.html)
- [Memory Profiling](https://pypi.org/project/memory-profiler/)
- [Load Testing Tools](https://locust.io/)
