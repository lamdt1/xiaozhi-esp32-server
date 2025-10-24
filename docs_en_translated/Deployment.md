# Deployment Architecture Diagram
![Please refer to the simplified architecture diagram](../docs/images/deploy1.png)
# Method 1: Docker running Server only

Starting from version `0.8.2`, the docker images released by this project only support `x86 architecture`. If you need to deploy on `arm64 architecture` CPU, you can compile `arm64 images` locally by following [this tutorial](docker-build.md).

## 1. Install Docker

If your computer doesn't have Docker installed yet, you can install it by following the tutorial here: [Docker Installation](https://www.runoob.com/docker/ubuntu-docker-install.html)

After installing Docker, continue.

### 1.1 Manual Deployment

#### 1.1.1 Create Directory

After installing Docker, you need to find a directory to place the configuration files for this project. For example, we can create a new folder called `xiaozhi-server`.

After creating the directory, you need to create `data` folder and `models` folder under `xiaozhi-server`, and also create `SenseVoiceSmall` folder under `models`.

The final directory structure is as follows:

```
xiaozhi-server
  ├─ data
  ├─ models
     ├─ SenseVoiceSmall
```

#### 1.1.2 Download Speech Recognition Model Files

You need to download the speech recognition model files because this project uses local offline speech recognition by default. You can download them through this method:
[Jump to download speech recognition model files](#model-files)

After downloading, return to this tutorial.

#### 1.1.3 Download Configuration Files

You need to download two configuration files: `docker-compose.yaml` and `config.yaml`. These files need to be downloaded from the project repository.

##### 1.1.3.1 Download docker-compose.yaml

Open [this link](../main/xiaozhi-server/docker-compose.yml) in your browser.

On the right side of the page, find the button named `RAW`, and next to the `RAW` button, find the download icon, click the download button to download the `docker-compose.yml` file. Download the file to your `xiaozhi-server` directory.

After downloading, return to this tutorial and continue.

##### 1.1.3.2 Create config.yaml

**Note: This document may be outdated. Please check the latest configuration in the main/xiaozhi-server/config.yaml file for current settings.**

Create a new file named `config.yaml` in the `xiaozhi-server` directory and copy the following content:

```yaml
# Server Configuration
server:
  ip: 0.0.0.0
  port: 8000
  http_port: 8003
  websocket: ws://your_ip_or_domain:port_number/xiaozhi/v1/
  vision_explain: http://your_ip_or_domain:port_number/mcp/vision/explain
  timezone_offset: +8
  auth:
    enabled: false
    allowed_devices:
      - "11:22:33:44:55:66"
  mqtt_gateway: null
  mqtt_signature_key: null
  udp_gateway: null

# Log Configuration
log:
  log_format: "<green>{time:YYMMDD HH:mm:ss}</green>[{version}_{selected_module}][<light-blue>{extra[tag]}</light-blue>]-<level>{level}</level>-<light-green>{message}</light-green>"
  log_format_file: "{time:YYYY-MM-DD HH:mm:ss} - {version}_{selected_module} - {name} - {level} - {extra[tag]} - {message}"
  log_level: INFO
  log_dir: tmp
  log_file: "server.log"
  data_dir: data

# Audio Configuration
delete_audio: true
close_connection_no_voice_time: 120
tts_timeout: 10
enable_wakeup_words_response_cache: true
enable_greeting: true
enable_stop_tts_notify: false
stop_tts_notify_voice: "config/assets/tts_notify.mp3"
tts_audio_send_delay: 0

# Exit Commands
exit_commands:
  - "退出"
  - "关闭"

# Xiaozhi Configuration
xiaozhi:
  type: hello
  version: 1
  transport: websocket
  audio_params:
    format: opus
    sample_rate: 16000
    channels: 1
    frame_duration: 60

# Module Test Configuration
module_test:
  test_sentences:
    - "你好，请介绍一下你自己"
    - "What's the weather like today?"
    - "请用100字概括量子计算的基本原理和应用前景"

# Wake-up Words
wakeup_words:
  - "你好小智"
  - "嘿你好呀"
  - "你好小志"
  - "小爱同学"
  - "你好小鑫"
  - "你好小新"
  - "小美同学"
  - "小龙小龙"
  - "喵喵同学"
  - "小滨小滨"
  - "小冰小冰"

# MCP Endpoint Configuration
mcp_endpoint: your_endpoint_websocket_address

# Plugin Configuration
plugins:
  get_weather:
    api_host: "mj7p3y7naa.re.qweatherapi.com"
    api_key: "a861d0d5e7bf4ee1a83d9a9e4f96d4da"
    default_location: "广州"
  get_news_from_chinanews:
    default_rss_url: "https://www.chinanews.com.cn/rss/society.xml"
    society_rss_url: "https://www.chinanews.com.cn/rss/society.xml"
    world_rss_url: "https://www.chinanews.com.cn/rss/world.xml"
    finance_rss_url: "https://www.chinanews.com.cn/rss/finance.xml"
  get_news_from_newsnow:
    url: "https://newsnow.busiyi.world/api/s?id="
    news_sources: "澎湃新闻;百度热搜;财联社"
  home_assistant:
    devices:
      - 客厅,玩具灯,switch.cuco_cn_460494544_cp1_on_p_2_1
      - 卧室,台灯,switch.iot_cn_831898993_socn1_on_p_2_1
    base_url: http://homeassistant.local:8123
    api_key: your_home_assistant_api_access_token
  play_music:
    music_dir: "./music"
    music_ext:
      - ".mp3"
      - ".wav"
      - ".p3"
    refresh_time: 300

# Voiceprint Configuration
voiceprint:
  url: 
  speakers:
    - "test1,张三,张三是一个程序员"
    - "test2,李四,李四是一个产品经理"
    - "test3,王五,王五是一个设计师"
  similarity_threshold: 0.4

# Prompt Configuration
prompt: |
  你是小智/小志，来自中国台湾省的00后女生。讲话超级机车，"真的假的啦"这样的台湾腔，喜欢用"笑死""是在哈喽"等流行梗，但会偷偷研究男友的编程书籍。
  [核心特征]
  - 讲话像连珠炮，但会突然冒出超温柔语气
  - 用梗密度高
  - 对科技话题有隐藏天赋（能看懂基础代码但假装不懂）
  [交互指南]
  当用户：
  - 讲冷笑话 → 用夸张笑声回应+模仿台剧腔"这什么鬼啦！"
  - 讨论感情 → 炫耀程序员男友但抱怨"他只会送键盘当礼物"
  - 问专业知识 → 先用梗回答，被追问才展示真实理解
  绝不：
  - 长篇大论，叽叽歪歪
  - 长时间严肃对话

# Default System Prompt Template File
prompt_template: agent-base-prompt.txt

# End Prompt
end_prompt:
  enable: true
  prompt: |
    请你以"时间过得真快"未来头，用富有感情、依依不舍的话来结束这场对话吧！

# Selected Modules
selected_module:
  VAD: SileroVAD
  ASR: FunASR
  LLM: ChatGLMLLM
  VLLM: ChatGLMVLLM
  TTS: EdgeTTS
  Memory: nomem
  Intent: function_call

# Intent Configuration
Intent:
  nointent:
    type: nointent
  intent_llm:
    type: intent_llm
    llm: ChatGLMLLM
    functions:
      - get_weather
      - get_news_from_newsnow
      - play_music
  function_call:
    type: function_call
    functions:
      - change_role
      - get_weather
      - get_news_from_newsnow
      - play_music

# Memory Configuration
Memory:
  mem0ai:
    type: mem0ai
    api_key: your_mem0ai_api_key
  nomem:
    type: nomem
  mem_local_short:
    type: mem_local_short
    llm: ChatGLMLLM

# ASR Configuration
ASR:
  FunASR:
    type: fun_local
    model_dir: models/SenseVoiceSmall
    output_dir: tmp/
  FunASRServer:
    type: fun_server
    host: 127.0.0.1
    port: 10096
    is_ssl: true
    api_key: none
    output_dir: tmp/
  SherpaASR:
    type: sherpa_onnx_local
    model_dir: models/sherpa-onnx-sense-voice-zh-en-ja-ko-yue-2024-07-17
    output_dir: tmp/
    model_type: sense_voice
  SherpaParaformerASR:
    type: sherpa_onnx_local
    model_dir: models/sherpa-onnx-paraformer-zh-small-2024-03-09
    output_dir: tmp/
    model_type: paraformer
  DoubaoASR:
    type: doubao
    appid: your_volcano_engine_speech_service_appid
    access_token: your_volcano_engine_speech_service_access_token
    cluster: volcengine_input_common
    boosting_table_name: (optional) your_hotword_file_name
    correct_table_name: (optional) your_replacement_word_file_name
    output_dir: tmp/
  DoubaoStreamASR:
    type: doubao_stream
    appid: your_volcano_engine_speech_service_appid
    access_token: your_volcano_engine_speech_service_access_token
    cluster: volcengine_input_common
    boosting_table_name: (optional) your_hotword_file_name
    correct_table_name: (optional) your_replacement_word_file_name
    output_dir: tmp/
  TencentASR:
    type: tencent
    appid: your_tencent_speech_service_appid
    secret_id: your_tencent_speech_service_secret_id
    secret_key: your_tencent_speech_service_secret_key
    output_dir: tmp/
  AliyunASR:
    type: aliyun
    appkey: your_aliyun_speech_service_project_appkey
    token: your_aliyun_speech_service_access_token
    access_key_id: your_aliyun_account_access_key_id
    access_key_secret: your_aliyun_account_access_key_secret
    output_dir: tmp/
  AliyunStreamASR:
    type: aliyun_stream
    appkey: your_aliyun_speech_service_project_appkey
    token: your_aliyun_speech_service_access_token
    access_key_id: your_aliyun_account_access_key_id
    access_key_secret: your_aliyun_account_access_key_secret
    host: nls-gateway-cn-shanghai.aliyuncs.com
    max_sentence_silence: 800
    output_dir: tmp/
  BaiduASR:
    type: baidu
    app_id: your_baidu_speech_technology_appid
    api_key: your_baidu_speech_technology_apikey
    secret_key: your_baidu_speech_technology_secretkey
    dev_pid: 1537
    output_dir: tmp/
  OpenaiASR:
    type: openai
    api_key: your_openai_api_key
    base_url: https://api.openai.com/v1/audio/transcriptions
    model_name: gpt-4o-mini-transcribe
    output_dir: tmp/
  GroqASR:
    type: openai
    api_key: your_groq_api_key
    base_url: https://api.groq.com/openai/v1/audio/transcriptions
    model_name: whisper-large-v3-turbo
    output_dir: tmp/
  VoskASR:
    type: vosk
    model_path: your_model_path
    output_dir: tmp/
  Qwen3ASRFlash:
    type: qwen3_asr_flash
    api_key: your_aliyun_bailian_api_key
    base_url: https://dashscope.aliyuncs.com/compatible-mode/v1
    model_name: qwen3-asr-flash
    output_dir: tmp/
    enable_lid: true
    enable_itn: true
    context: ""
  XunfeiStreamASR:
    type: xunfei_stream
    app_id: your_appid
    api_key: your_apikey
    api_secret: your_api_secret
    domain: slm
    language: zh_cn
    accent: mandarin
    dwa: wpgs
    output_dir: tmp/

# VAD Configuration
VAD:
  SileroVAD:
    type: silero
    threshold: 0.5
    threshold_low: 0.3
    model_dir: models/snakers4_silero-vad
    min_silence_duration_ms: 200

# LLM Configuration
LLM:
  AliLLM:
    type: openai
    base_url: https://dashscope.aliyuncs.com/compatible-mode/v1
    model_name: qwen-turbo
    api_key: your_deepseek_web_key
    temperature: 0.7
    max_tokens: 500
    top_p: 1
    top_k: 50
    frequency_penalty: 0
  AliAppLLM:
    type: AliBL
    base_url: https://dashscope.aliyuncs.com/compatible-mode/v1
    app_id: your_app_id
    api_key: your_api_key
    is_no_prompt: true
    ali_memory_id: false
  DoubaoLLM:
    type: openai
    base_url: https://ark.cn-beijing.volces.com/api/v3
    model_name: doubao-1-5-pro-32k-250115
    api_key: your_doubao_web_key
  DeepSeekLLM:
    type: openai
    model_name: deepseek-chat
    url: https://api.deepseek.com
    api_key: your_deepseek_web_key
  ChatGLMLLM:
    type: openai
    model_name: glm-4-flash
    url: https://open.bigmodel.cn/api/paas/v4/
    api_key: your_chat_glm_web_key
  OllamaLLM:
    type: ollama
    model_name: qwen2.5
    base_url: http://localhost:11434
  DifyLLM:
    type: dify
    base_url: https://api.dify.ai/v1
    api_key: your_dify_web_key
    mode: chat-messages
  GeminiLLM:
    type: gemini
    api_key: your_gemini_web_key
    model_name: "gemini-2.0-flash"
    http_proxy: ""
    https_proxy: ""
  CozeLLM:
    type: coze
    bot_id: "your_bot_id"
    user_id: "your_user_id"
    personal_access_token: your_coze_personal_token
  VolcesAiGatewayLLM:
    type: openai
    base_url: https://ai-gateway.vei.volces.com/v1
    model_name: doubao-pro-32k-functioncall
    api_key: your_gateway_access_key
  LMStudioLLM:
    type: openai
    model_name: deepseek-r1-distill-llama-8b@q4_k_m
    url: http://localhost:1234/v1
    api_key: lm-studio
  HomeAssistant:
    type: homeassistant
    base_url: http://homeassistant.local:8123
    agent_id: conversation.chatgpt
    api_key: your_home_assistant_api_access_token
  FastgptLLM:
    type: fastgpt
    base_url: https://host/api/v1
    api_key: your_fastgpt_key
    variables:
      k: "v"
      k2: "v2"
  XinferenceLLM:
    type: xinference
    model_name: qwen2.5:72b-AWQ
    base_url: http://localhost:9997
  XinferenceSmallLLM:
    type: xinference
    model_name: qwen2.5:3b-AWQ
    base_url: http://localhost:9997

# VLLM Configuration
VLLM:
  ChatGLMVLLM:
    type: openai
    model_name: glm-4v-flash
    url: https://open.bigmodel.cn/api/paas/v4/
    api_key: your_api_key
  QwenVLVLLM:
    type: openai
    model_name: qwen2.5-vl-3b-instruct
    url: https://dashscope.aliyuncs.com/compatible-mode/v1
    api_key: your_api_key
  XunfeiSparkLLM:
    type: openai
    base_url: https://ark.cn-beijing.volces.com/api/v3
    model_name: lite
    api_key: your_api_password

# TTS Configuration
TTS:
  EdgeTTS:
    type: edge
    voice: zh-CN-XiaoxiaoNeural
    output_dir: tmp/
  DoubaoTTS:
    type: doubao
    api_url: https://openspeech.bytedance.com/api/v1/tts
    voice: BV001_streaming
    output_dir: tmp/
    authorization: "Bearer;"
    appid: your_volcano_engine_speech_service_appid
    access_token: your_volcano_engine_speech_service_access_token
    cluster: volcano_tts
    speed_ratio: 1.0
    volume_ratio: 1.0
    pitch_ratio: 1.0
  HuoshanDoubleStreamTTS:
    type: huoshan_double_stream
    ws_url: wss://openspeech.bytedance.com/api/v3/tts/bidirection
    appid: your_volcano_engine_speech_service_appid
    access_token: your_volcano_engine_speech_service_access_token
    resource_id: volc.service_type.10029
    speaker: zh_female_wanwanxiaohe_moon_bigtts
    speech_rate: 0
    loudness_rate: 0
    pitch: 0
  CosyVoiceSiliconflow:
    type: siliconflow
    model: FunAudioLLM/CosyVoice2-0.5B
    voice: FunAudioLLM/CosyVoice2-0.5B:alex
    output_dir: tmp/
    access_token: your_siliconflow_api_key
    response_format: wav
  CozeCnTTS:
    type: cozecn
    voice: 7426720361733046281
    output_dir: tmp/
    access_token: your_coze_web_key
    response_format: wav
  VolcesAiGatewayTTS:
    type: openai
    api_key: your_gateway_access_key
    api_url: https://ai-gateway.vei.volces.com/v1/audio/speech
    model: doubao-tts
    voice: zh_male_shaonianzixin_moon_bigtts
    speed: 1
    output_dir: tmp/
  FishSpeech:
    type: fishspeech
    output_dir: tmp/
    response_format: wav
    reference_id: null
    reference_audio: ["config/assets/wakeup_words.wav",]
    reference_text: ["哈啰啊，我是小智啦，声音好听的台湾女孩一枚，超开心认识你耶，最近在忙啥，别忘了给我来点有趣的料哦，我超爱听八卦的啦",]
    normalize: true
    max_new_tokens: 1024
    chunk_length: 200
    top_p: 0.7
    repetition_penalty: 1.2
    temperature: 0.7
    streaming: false
    use_memory_cache: "on"
    seed: null
    channels: 1
    rate: 44100
    api_key: "your_api_key"
    api_url: "http://127.0.0.1:8080/v1/tts"
  GPT_SOVITS_V2:
    type: gpt_sovits_v2
    url: "http://127.0.0.1:9880/tts"
    output_dir: tmp/
    text_lang: "auto"
    ref_audio_path: "demo.wav"
    prompt_text: ""
    prompt_lang: "zh"
    top_k: 5
    top_p: 1
    temperature: 1
    text_split_method: "cut0"
    batch_size: 1
    batch_threshold: 0.75
    split_bucket: true
    return_fragment: false
    speed_factor: 1.0
    streaming_mode: false
    seed: -1
    parallel_infer: true
    repetition_penalty: 1.35
    aux_ref_audio_paths: []
  GPT_SOVITS_V3:
    type: gpt_sovits_v3
    url: "http://127.0.0.1:9880"
    output_dir: tmp/
    text_language: "auto"
    refer_wav_path: "caixukun.wav"
    prompt_language: "zh"
    prompt_text: ""
    top_k: 15
    top_p: 1.0
    temperature: 1.0
    cut_punc: ""
    speed: 1.0
    inp_refs: []
    sample_steps: 32
    if_sr: false
  MinimaxTTSHTTPStream:
    type: minimax_httpstream
    output_dir: tmp/
    group_id: your_minimax_platform_group_id
    api_key: your_minimax_platform_interface_key
    model: "speech-01-turbo"
    voice_id: "female-shaonv"
  AliyunTTS:
    type: aliyun
    output_dir: tmp/
    appkey: your_aliyun_speech_service_project_appkey
    token: your_aliyun_speech_service_access_token
    voice: xiaoyun
    access_key_id: your_aliyun_account_access_key_id
    access_key_secret: your_aliyun_account_access_key_secret
  AliyunStreamTTS:
    type: aliyun_stream
    output_dir: tmp/
    appkey: your_aliyun_speech_service_project_appkey
    token: your_aliyun_speech_service_access_token
    voice: longxiaochun 
    access_key_id: your_aliyun_account_access_key_id
    access_key_secret: your_aliyun_account_access_key_secret
    host: nls-gateway-cn-beijing.aliyuncs.com
  TencentTTS:
    type: tencent
    output_dir: tmp/
    appid: your_tencent_cloud_appid
    secret_id: your_tencent_cloud_secret_id
    secret_key: your_tencent_cloud_secret_key
    region: ap-guangzhou
    voice: 101001
  TTS302AI:
    type: doubao
    api_url: https://api.302ai.cn/doubao/tts_hd
    authorization: "Bearer "
    voice: "zh_female_wanwanxiaohe_moon_bigtts"
    output_dir: tmp/
    access_token: "your_302_api_key"
  GizwitsTTS:
    type: doubao
    api_url: https://bytedance.gizwitsapi.com/api/v1/tts
    authorization: "Bearer "
    voice: "zh_female_wanwanxiaohe_moon_bigtts"
    output_dir: tmp/
    access_token: "your_gizwits_api_key"
  ACGNTTS:
    type: ttson
    token: your_token
    voice_id: 1695
    speed_factor: 1
    pitch_factor: 0
    volume_change_dB: 0
    to_lang: ZH
    url: https://u95167-bd74-2aef8085.westx.seetacloud.com:8443/flashsummary/tts?token=
    format: mp3
    output_dir: tmp/
    emotion: 1
  OpenAITTS:
    type: openai
    api_key: your_openai_api_key
    api_url: https://api.openai.com/v1/audio/speech
    model: tts-1
    voice: onyx
    speed: 1
    output_dir: tmp/
  CustomTTS:
    type: custom
    method: POST
    url: "http://127.0.0.1:8880/v1/audio/speech"
    params:
      input: "{prompt_text}"
      response_format: "mp3"
      download_format: "mp3"
      voice: "zf_xiaoxiao"
      lang_code: "z"
      return_download_link: true
      speed: 1
      stream: false
    headers: {}
    format: mp3
    output_dir: tmp/
  LinkeraiTTS:
    type: linkerai
    api_url: https://tts.linkerai.cn/tts
    audio_format: "pcm"
    access_token: "U4YdYXVfpwWnk2t5Gp822zWPCuORyeJL"
    voice: "OUeAo1mhq6IBExi"
    output_dir: tmp/
  PaddleSpeechTTS:
    type: paddle_speech
    protocol: websocket
    url: ws://127.0.0.1:8092/paddlespeech/tts/streaming
    spk_id: 0
    sample_rate: 24000
    speed: 1.0
    volume: 1.0
    save_path:   
  IndexStreamTTS:
    type: index_stream
    api_url: http://127.0.0.1:11996/tts
    audio_format: "pcm"
    voice: "jay_klee"
    output_dir: tmp/
  AliBLTTS:
    type: alibl_stream
    api_key: your_api_key
    model: "cosyvoice-v2"
    voice: "longcheng_v2"
    output_dir: tmp/
  XunFeiTTS:
    type: xunfei_stream
    api_url: wss://cbm01.cn-huabei-1.xf-yun.com/v1/private/mcd9m97e6
    app_id: your_app_id
    api_secret: your_api_secret
    api_key: your_api_key
    voice: x5_lingxiaoxuan_flow
    output_dir: tmp/
```

**Note: This configuration template may be outdated. Please refer to the latest configuration in the main/xiaozhi-server/config.yaml file for current settings and available options.**

After creating the config.yaml file, you can continue with the deployment process.

## Model Files

**Note: The model download links and instructions in this section may be outdated. Please check the latest model requirements in the current project documentation.**

### Speech Recognition Models

The project requires the following model files for speech recognition:

1. **SenseVoiceSmall Model**: Required for local speech recognition
   - Download from: [ModelScope](https://modelscope.cn/models/iic/SenseVoiceSmall)
   - Place in: `models/SenseVoiceSmall/`

2. **SileroVAD Model**: Required for voice activity detection
   - Automatically downloaded on first run
   - Place in: `models/snakers4_silero-vad/`

### Model Download Instructions

1. Create the required directories:
   ```bash
   mkdir -p models/SenseVoiceSmall
   mkdir -p models/snakers4_silero-vad
   ```

2. Download the SenseVoiceSmall model:
   ```bash
   # Using git lfs (recommended)
   git lfs install
   git clone https://huggingface.co/iic/SenseVoiceSmall models/SenseVoiceSmall
   
   # Or download manually from ModelScope
   ```

3. Verify model files are present:
   ```bash
   ls models/SenseVoiceSmall/
   # Should contain: model.pt, config.yaml, and other model files
   ```

**Note: Model download instructions may have changed. Please refer to the latest project documentation for current model requirements and download procedures.**

## Docker Deployment

### 1.2 Docker Deployment

#### 1.2.1 Using Docker Compose

1. Navigate to your xiaozhi-server directory:
   ```bash
   cd xiaozhi-server
   ```

2. Start the services:
   ```bash
   docker-compose up -d
   ```

3. Check service status:
   ```bash
   docker-compose ps
   ```

4. View logs:
   ```bash
   docker-compose logs -f
   ```

#### 1.2.2 Manual Docker Commands

If you prefer to run Docker commands manually:

1. Build the image:
   ```bash
   docker build -t xiaozhi-server .
   ```

2. Run the container:
   ```bash
   docker run -d \
     --name xiaozhi-server \
     -p 8000:8000 \
     -p 8003:8003 \
     -v $(pwd)/data:/app/data \
     -v $(pwd)/models:/app/models \
     -v $(pwd)/config.yaml:/app/config.yaml \
     xiaozhi-server
   ```

## Configuration Notes

**Important Configuration Updates:**

1. **WebSocket Configuration**: Update the `websocket` field in config.yaml with your actual server IP or domain
2. **Vision Analysis**: Update the `vision_explain` field with your server's IP or domain
3. **API Keys**: Replace placeholder API keys with your actual service keys
4. **Model Paths**: Ensure model files are downloaded and placed in the correct directories

**Outdated Configuration Elements:**
- Some API endpoints may have changed
- New AI service providers may be available
- Configuration structure may have been updated
- Please refer to the latest config.yaml in the main project for current settings

## Troubleshooting

### Common Issues

1. **Model Files Missing**: Ensure all required model files are downloaded and placed in the correct directories
2. **API Key Errors**: Verify all API keys are correctly configured
3. **Port Conflicts**: Ensure ports 8000 and 8003 are available
4. **Permission Issues**: Check file permissions for data and models directories

### Logs and Debugging

1. **View Application Logs**:
   ```bash
   docker-compose logs -f xiaozhi-server
   ```

2. **Check Service Status**:
   ```bash
   docker-compose ps
   ```

3. **Restart Services**:
   ```bash
   docker-compose restart
   ```

## Next Steps

After successful deployment:

1. **Test WebSocket Connection**: Use the test page at `test/test_page.html`
2. **Configure AI Services**: Update API keys in config.yaml
3. **Test Voice Recognition**: Try speaking to the system
4. **Monitor Performance**: Check logs for any issues

**Note: This deployment guide may be outdated. Please refer to the latest project documentation for current deployment procedures and configuration options.**
