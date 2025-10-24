# Frequently Asked Questions ❓

### 1. Why does Xiaozhi recognize Korean, Japanese, and English when I speak Chinese? 🇰🇷

**Suggestion**: Check if the `models/SenseVoiceSmall` directory already contains the `model.pt` file. If not, you need to download it. See here: [Download Speech Recognition Model Files](Deployment.md#model-files)

### 2. Why does "TTS task error - file not found" occur? 📁

**Suggestion**: Check if you have correctly installed the `libopus` and `ffmpeg` libraries using `conda`.

If not installed, install them:

```
conda install conda-forge::libopus
conda install conda-forge::ffmpeg
```

### 3. TTS frequently fails and times out ⏰

**Suggestion**: If `EdgeTTS` frequently fails, first check if you are using a proxy (VPN). If you are, try turning off the proxy and try again;  
If using Volcano Engine's Doubao TTS and it frequently fails, it's recommended to use the paid version, as the test version only supports 2 concurrent connections.

### 4. Can connect to self-built server using WiFi, but can't connect in 4G mode 🔐

**Reason**: Xiaoge's firmware requires secure connections in 4G mode.

**Solution**: Currently there are two methods to solve this. Choose either one:

1. **Modify the code**. Refer to this video for solution: https://www.bilibili.com/video/BV18MfTYoE85

2. **Use HTTPS/WSS**: Configure your server to use HTTPS and WSS protocols instead of HTTP and WS.

### 5. Why does the system respond slowly or not respond at all? 🐌

**Possible causes and solutions**:

- **Network latency**: Check your network connection speed
- **Server performance**: Monitor CPU and memory usage
- **Model loading**: Ensure all required models are properly loaded
- **API rate limits**: Check if you've exceeded service provider rate limits

### 6. How to improve speech recognition accuracy? 🎯

**Suggestions**:
- Use a good quality microphone
- Speak clearly and at normal volume
- Reduce background noise
- Ensure stable network connection
- Consider using local models for better privacy and performance

### 7. Why can't I connect to the WebSocket server? 🔌

**Troubleshooting steps**:
1. Check if the server is running: `docker-compose ps`
2. Verify the WebSocket URL in config.yaml
3. Check firewall settings
4. Ensure the correct port is open (default: 8000)
5. Test connection using the test page: `test/test_page.html`

### 8. How to configure multiple AI service providers? 🤖

**Configuration steps**:
1. Edit `config.yaml`
2. Update the `selected_module` section
3. Configure API keys for each service
4. Restart the server: `docker-compose restart`

### 9. Memory usage is too high 💾

**Optimization suggestions**:
- Use smaller models when possible
- Enable model caching
- Monitor memory usage: `docker stats`
- Consider using cloud-based services for heavy processing

### 10. How to add custom wake-up words? 🎤

**Configuration**:
1. Edit the `wakeup_words` section in `config.yaml`
2. Add your custom wake-up phrases
3. Restart the server
4. Test with your new wake-up words

### 11. Plugin system not working 🔌

**Troubleshooting**:
1. Check plugin configuration in `config.yaml`
2. Verify API keys for external services
3. Check plugin logs in the application logs
4. Ensure plugin files are in the correct directory

### 12. How to update the system? 🔄

**Update process**:
1. Backup your configuration: `cp config.yaml config.yaml.backup`
2. Pull latest changes: `git pull`
3. Update Docker images: `docker-compose pull`
4. Restart services: `docker-compose restart`
5. Verify configuration compatibility

### 13. Security concerns 🔒

**Security recommendations**:
1. Use strong authentication keys
2. Enable HTTPS/WSS in production
3. Regularly update dependencies
4. Monitor access logs
5. Use environment variables for sensitive data

### 14. Performance optimization ⚡

**Optimization tips**:
1. Use SSD storage for models
2. Allocate sufficient RAM
3. Use GPU acceleration when available
4. Optimize network configuration
5. Monitor system resources

### 15. Integration with Home Assistant 🏠

**Setup steps**:
1. Configure Home Assistant API in `config.yaml`
2. Set up device entities
3. Test device control commands
4. Verify voice control functionality

**Note: Some troubleshooting steps may be outdated. Please refer to the latest project documentation for current solutions and configuration options.**

## Getting Help

If you encounter issues not covered in this FAQ:

1. **Check the logs**: `docker-compose logs -f`
2. **Review configuration**: Verify all settings in `config.yaml`
3. **Test components**: Use the test page to verify WebSocket connection
4. **Community support**: Check project issues and discussions
5. **Documentation**: Refer to the latest project documentation

## Configuration Validation

Before reporting issues, please verify:

- [ ] All required model files are downloaded
- [ ] API keys are correctly configured
- [ ] Network connectivity is working
- [ ] Required ports are open
- [ ] Docker services are running properly
- [ ] Configuration file syntax is correct

**Note: This FAQ may be outdated. Please refer to the latest project documentation and community discussions for current solutions.**
