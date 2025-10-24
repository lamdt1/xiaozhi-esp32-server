# Xiaozhi-ESP32-Server Brownfield Architecture Document

## Introduction

This document captures the CURRENT STATE of the xiaozhi-esp32-server codebase, including technical debt, workarounds, and real-world patterns. It serves as a reference for AI agents working on enhancements.

### Document Scope

Comprehensive documentation of entire system - a multi-component voice AI platform for ESP32 devices with WebSocket communication, AI service integration, and management interfaces.

### Change Log

| Date   | Version | Description                 | Author    |
| ------ | ------- | --------------------------- | --------- |
| 2025-01-27 | 1.0     | Initial brownfield analysis | BMad Master |

## Quick Reference - Key Files and Entry Points

### Critical Files for Understanding the System

- **Main Entry**: `main/xiaozhi-server/app.py` (Python AI server entry point)
- **WebSocket Server**: `main/xiaozhi-server/core/websocket_server.py` (ESP32 communication)
- **Connection Handler**: `main/xiaozhi-server/core/connection.py` (Per-device session management)
- **Configuration**: `main/xiaozhi-server/config.yaml` (AI service configuration)
- **Manager API**: `main/manager-api/src/main/java/xiaozhi/` (Java Spring Boot backend)
- **Manager Web**: `main/manager-web/src/` (Vue.js frontend)
- **Manager Mobile**: `main/manager-mobile/src/` (uni-app mobile interface)

### Key AI Service Providers

- **ASR Providers**: `main/xiaozhi-server/core/providers/asr/` (Speech recognition)
- **LLM Providers**: `main/xiaozhi-server/core/providers/llm/` (Language models)
- **TTS Providers**: `main/xiaozhi-server/core/providers/tts/` (Text-to-speech)
- **VAD Providers**: `main/xiaozhi-server/core/providers/vad/` (Voice activity detection)

## High Level Architecture

### Technical Summary

The xiaozhi-esp32-server is a distributed voice AI platform consisting of four main components:

1. **xiaozhi-server** (Python) - Core AI processing engine
2. **manager-api** (Java Spring Boot) - Management backend with database
3. **manager-web** (Vue.js) - Web management interface  
4. **manager-mobile** (uni-app) - Mobile management interface

### Actual Tech Stack

| Category  | Technology | Version | Notes                      |
| --------- | ---------- | ------- | -------------------------- |
| AI Server | Python     | 3.x     | Async WebSocket server     |
| AI Server | asyncio    | -       | Async programming framework |
| AI Server | websockets | 14.2    | WebSocket communication    |
| AI Server | torch      | 2.2.2   | ML/AI model support        |
| AI Server | funasr     | 1.2.3   | Local speech recognition    |
| AI Server | openai     | 2.5.0   | OpenAI API client          |
| Backend   | Java       | 21      | Spring Boot 3.4.3         |
| Backend   | Spring Boot| 3.4.3   | REST API framework         |
| Backend   | MyBatis-Plus| 3.5.5  | ORM framework              |
| Backend   | MySQL      | -       | Primary database           |
| Backend   | Redis      | -       | Caching layer              |
| Backend   | Apache Shiro| 2.0.2  | Security framework          |
| Frontend  | Vue.js     | 2.x     | Web UI framework           |
| Frontend  | Element UI | -       | UI component library       |
| Frontend  | Vuex       | -       | State management           |
| Mobile    | uni-app    | v3      | Cross-platform framework   |
| Mobile    | Vue 3      | 3.x     | Mobile UI framework        |
| Mobile    | Vite       | -       | Build tool                 |

### Repository Structure Reality Check

- Type: Monorepo with multiple language components
- Package Managers: pip (Python), Maven (Java), npm (Node.js), pnpm (uni-app)
- Notable: Each component has its own build system and dependencies

## Source Tree and Module Organization

### Project Structure (Actual)

```text
xiaozhi-esp32-server/
├── main/
│   ├── xiaozhi-server/          # Python AI server (Port 8000)
│   │   ├── app.py               # Main entry point
│   │   ├── config.yaml          # AI service configuration
│   │   ├── core/                # Core AI processing
│   │   │   ├── websocket_server.py    # WebSocket server
│   │   │   ├── connection.py          # Per-device handler
│   │   │   ├── providers/             # AI service providers
│   │   │   │   ├── asr/               # Speech recognition
│   │   │   │   ├── llm/               # Language models
│   │   │   │   ├── tts/               # Text-to-speech
│   │   │   │   ├── vad/               # Voice activity detection
│   │   │   │   └── tools/             # Plugin system
│   │   │   └── handle/                # Message handlers
│   │   ├── plugins_func/         # Plugin functions
│   │   └── requirements.txt      # Python dependencies
│   ├── manager-api/              # Java Spring Boot API (Port 8002)
│   │   ├── src/main/java/xiaozhi/
│   │   │   ├── modules/          # Business modules
│   │   │   │   ├── agent/        # Agent management
│   │   │   │   ├── config/       # Configuration
│   │   │   │   ├── device/       # Device management
│   │   │   │   ├── model/        # AI model config
│   │   │   │   └── sys/          # System management
│   │   │   └── common/           # Shared utilities
│   │   └── pom.xml               # Maven dependencies
│   ├── manager-web/              # Vue.js frontend (Port 8001)
│   │   ├── src/
│   │   │   ├── apis/             # API communication
│   │   │   ├── components/       # Vue components
│   │   │   ├── views/            # Page views
│   │   │   └── store/            # Vuex state management
│   │   └── package.json          # Node.js dependencies
│   └── manager-mobile/            # uni-app mobile (Cross-platform)
│       ├── src/
│       │   ├── pages/            # Mobile pages
│       │   ├── components/       # Mobile components
│       │   └── store/            # Pinia state management
│       └── package.json          # pnpm dependencies
├── docs/                         # Documentation
└── docker-compose.yml            # Docker deployment
```

### Key Modules and Their Purpose

- **WebSocket Communication**: `core/websocket_server.py` + `core/connection.py` - Handles real-time ESP32 communication
- **AI Service Providers**: `core/providers/` - Pluggable AI service architecture
- **Message Processing**: `core/handle/` - Message routing and processing
- **Plugin System**: `plugins_func/` - Extensible function system
- **Management API**: `manager-api/src/main/java/xiaozhi/modules/` - REST API for configuration
- **Web Interface**: `manager-web/src/` - Vue.js management console
- **Mobile Interface**: `manager-mobile/src/` - Cross-platform mobile app

## Data Models and APIs

### Data Models

Instead of duplicating, reference actual model files:

- **Device Model**: See `manager-api/src/main/java/xiaozhi/modules/device/entity/`
- **User Model**: See `manager-api/src/main/java/xiaozhi/modules/sys/entity/`
- **Model Config**: See `manager-api/src/main/java/xiaozhi/modules/model/entity/`
- **Agent Config**: See `manager-api/src/main/java/xiaozhi/modules/agent/entity/`

### API Specifications

- **OpenAPI Spec**: Available at `http://localhost:8002/xiaozhi/doc.html` (Knife4j)
- **REST Endpoints**: Organized by modules in `manager-api/src/main/java/xiaozhi/modules/`
- **WebSocket Protocol**: Documented in `docs/` (Chinese documentation)

## Technical Debt and Known Issues

### Critical Technical Debt

1. **Mixed Language Architecture**: Python AI server + Java API + Vue.js frontend creates deployment complexity
2. **Configuration Management**: Multiple config files (`config.yaml`, `application.yml`, `.env` files)
3. **AI Service Integration**: Provider pattern is good but many providers have inconsistent interfaces
4. **WebSocket Connection Handling**: Each connection creates a new handler instance - potential memory issues
5. **Plugin System**: Functions are loaded dynamically but error handling is inconsistent

### Workarounds and Gotchas

- **Authentication**: JWT tokens are generated but validation is inconsistent across components
- **Database Migrations**: Liquibase is used but manual SQL files exist in `src/main/resources/db/`
- **AI Model Loading**: Models are loaded on startup - memory usage can be high
- **WebSocket Reconnection**: No automatic reconnection logic for ESP32 devices
- **Configuration Sync**: `xiaozhi-server` polls `manager-api` for config updates

## Integration Points and External Dependencies

### External Services

| Service  | Purpose  | Integration Type | Key Files                      |
| -------- | -------- | ---------------- | ------------------------------ |
| OpenAI   | LLM/TTS  | REST API         | `core/providers/llm/openai/`   |
| FunASR   | ASR      | Local Model      | `core/providers/asr/fun_local.py` |
| EdgeTTS  | TTS      | REST API         | `core/providers/tts/edge.py`   |
| SileroVAD| VAD      | Local Model      | `core/providers/vad/silero.py` |
| MySQL    | Database | JDBC             | `manager-api/src/main/resources/` |
| Redis    | Cache    | Jedis            | `manager-api/src/main/java/xiaozhi/common/config/` |

### Internal Integration Points

- **ESP32 Communication**: WebSocket on port 8000, binary audio + JSON messages
- **Frontend-Backend**: REST API on port 8002, JSON over HTTP
- **Config Sync**: `xiaozhi-server` → `manager-api` via HTTP POST
- **Mobile Sync**: Same REST API as web frontend

## Development and Deployment

### Local Development Setup

1. **Python Environment**: 
   ```bash
   cd main/xiaozhi-server
   pip install -r requirements.txt
   python app.py
   ```

2. **Java Environment**:
   ```bash
   cd main/manager-api
   mvn spring-boot:run
   ```

3. **Vue.js Environment**:
   ```bash
   cd main/manager-web
   npm install
   npm run serve
   ```

4. **uni-app Environment**:
   ```bash
   cd main/manager-mobile
   pnpm install
   pnpm dev
   ```

### Build and Deployment Process

- **Docker Deployment**: `docker-compose.yml` for full stack
- **Individual Services**: Each component has its own build process
- **Environments**: Development, staging, production configs exist

## Testing Reality

### Current Test Coverage

- Unit Tests: Minimal (Java has some, Python has none)
- Integration Tests: None
- E2E Tests: None
- Manual Testing: Primary QA method

### Running Tests

```bash
# Java tests
cd main/manager-api
mvn test

# No Python tests currently exist
# No frontend tests currently exist
```

## AI Service Provider Architecture

### Provider Pattern Implementation

The system uses a provider pattern for AI services:

```python
# Base provider interface
class ASRProviderBase:
    async def transcribe(self, audio_chunk: bytes) -> str:
        pass

# Specific implementations
class FunASRProvider(ASRProviderBase):
    # Local FunASR implementation
    
class OpenAIASRProvider(ASRProviderBase):
    # OpenAI API implementation
```

### Supported AI Services

**ASR (Speech Recognition)**:
- FunASR (local)
- OpenAI Whisper
- Baidu ASR
- Aliyun ASR
- Tencent ASR
- Doubao ASR

**LLM (Language Models)**:
- OpenAI GPT
- ChatGLM
- Gemini
- Ollama
- Dify
- AliLLM
- DoubaoLLM

**TTS (Text-to-Speech)**:
- EdgeTTS
- OpenAI TTS
- DoubaoTTS
- AliyunTTS
- FishSpeech
- GPT-SoVITS

**VAD (Voice Activity Detection)**:
- SileroVAD (local)

## WebSocket Communication Protocol

### Message Types

1. **Hello Message**: Initial connection handshake
2. **Audio Message**: Binary audio data from ESP32
3. **Text Message**: Processed text responses
4. **Control Messages**: Start/stop listening, abort TTS

### Audio Processing Flow

1. ESP32 sends binary audio → WebSocket
2. VAD detects speech activity
3. ASR converts audio to text
4. LLM processes text and generates response
5. TTS converts response to audio
6. Audio sent back to ESP32 via WebSocket

## Plugin System

### Function Registration

```python
# plugins_func/functions/get_weather.py
def get_weather(location: str) -> str:
    """Get weather information for a location"""
    # Implementation
    return weather_data
```

### LLM Function Calling

The system supports OpenAI-style function calling where the LLM can invoke registered functions based on user requests.

## Appendix - Useful Commands and Scripts

### Frequently Used Commands

```bash
# Start all services
docker-compose up -d

# Start individual services
cd main/xiaozhi-server && python app.py
cd main/manager-api && mvn spring-boot:run
cd main/manager-web && npm run serve

# Database operations
cd main/manager-api && mvn liquibase:update
```

### Debugging and Troubleshooting

- **Logs**: Check `main/xiaozhi-server/tmp/server.log`
- **WebSocket Testing**: Use `main/xiaozhi-server/test/test_page.html`
- **API Documentation**: Visit `http://localhost:8002/xiaozhi/doc.html`
- **Common Issues**: See `docs/FAQ.md`

### Configuration Files

- **AI Server**: `main/xiaozhi-server/config.yaml`
- **Manager API**: `main/manager-api/src/main/resources/application.yml`
- **Manager Web**: `main/manager-web/.env.development`
- **Manager Mobile**: `main/manager-mobile/.env`

## Security Considerations

### Authentication

- JWT tokens for API authentication
- Device ID-based WebSocket authentication
- Shiro security framework for Java backend

### Data Protection

- API keys stored in configuration files
- No sensitive data in logs
- HTTPS support for production deployment

## Performance Characteristics

### Memory Usage

- Python server: ~500MB-2GB (depending on AI models)
- Java API: ~200-500MB
- Database: Depends on device count and configuration

### Scalability

- WebSocket connections: Limited by server memory
- Database: MySQL with connection pooling
- Caching: Redis for frequently accessed data

This document reflects the actual state of the xiaozhi-esp32-server system as of January 2025, including all technical debt, workarounds, and real-world implementation patterns.
