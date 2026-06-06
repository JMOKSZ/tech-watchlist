# Open-Source TTS Tools Comparison (vs VoxCPM2)

*Research date: June 2026. Focus on actively maintained tools (2025-2026).*

## Summary Table

| Feature | **VoxCPM2** | **CosyVoice 3** | **GPT-SoVITS v2** | **Fish Speech S2** | **ChatTTS** | **MaskGCT (Amphion)** | **Bark** | **XTTS v2** |
|---|---|---|---|---|---|---|---|---|
| **GitHub Stars** | - | 21.5k | 58.4k | 30.7k | 39.4k | 9.8k (Amphion) | 39.1k | 45.5k (Coqui TTS) |
| **Organization** | BAAI/Zhipu | Alibaba FunAudioLLM | Community (RVC-Boss) | Fish Audio | 2noise | OpenMMLab | Suno AI | Coqui (shut down) |
| **Model Size** | 0.5B (base) | 0.3B / 0.5B | ~0.5B (GPT+SoVITS) | 4.56B (Dual-AR) | Not disclosed (~0.5B) | ~1B+ (multi-model) | ~1.2B (3 models) | ~1.6B (est.) |
| **Languages** | zh, en + more | 9 langs + 18+ CN dialects | zh, en, ja, ko, yue | 80+ languages | zh, en only | 6+ (Emilia dataset) | 13 languages | 16 languages |
| **Voice Cloning** | ✅ Zero-shot | ✅ Zero-shot | ✅ Few-shot (1min) + Zero-shot (5s) | ✅ Zero-shot (10-30s) | ❌ Random spk only | ✅ Zero-shot (Vevo/Metis) | ❌ Presets only | ✅ Zero-shot (3-10s) |
| **Voice Design / Emotion Control** | ✅ Instruct-based | ✅ Instruct (lang, emotion, speed, volume) | ❌ Limited | ✅ 15,000+ inline tags, sub-word control | ✅ [laugh], [uv_break], pauses | ❌ Limited | ✅ Non-speech sounds, music | ❌ Limited |
| **Streaming** | ✅ | ✅ (Bi-streaming, 150ms latency) | ✅ | ✅ (SGLang, ~100ms TTFA) | ✅ | ❌ | ❌ | ✅ (<200ms) |
| **License** | Open-source (待确认) | Apache 2.0 | MIT | Fish Audio Research License (restrictive) | AGPLv3 (code) / CC BY-NC 4.0 (model) | MIT | MIT | MPL 2.0 (code) / Coqui license (model, NC) |
| **Latest Release / Update** | 2026 Q1 | May 2026 (v3.0, active) | Jun 2025 (v2 ProPlus, active) | May 2025 (v1.5.1, S2 Pro active) | Apr 2026 (v0.2.5, active) | Mar 2026 (active commits) | Apr 2024 (⚠️ inactive) | Dec 2023 (⚠️ inactive) |
| **First Release** | 2025 H2 | Jul 2024 | Jan 2024 | Oct 2023 | May 2024 | Nov 2023 | Apr 2023 | May 2020 |
| **Key Limitation** | Newer, smaller ecosystem | Needs ttsfrd for best text normalization | Fewer languages; needs fine-tuning for best quality | Restrictive license; very large model (4.56B) | Only zh/en; no voice cloning; non-commercial model | Complex toolkit; research-focused | Not maintained; no voice cloning; slow | Coqui shut down; not maintained; model license restrictive |

## Detailed Breakdown

### 1. CosyVoice (阿里巴巴通义)
- **Repo**: [FunAudioLLM/CosyVoice](https://github.com/FunAudioLLM/CosyVoice)
- **Stars**: 21,476
- **Architecture**: LLM-based, autoregressive + flow matching
- **Versions**: v1.0 (Jul 2024, 300M), v2.0 (Dec 2024, 0.5B), v3.0 (Dec 2025, 0.5B + RL)
- **Key Strengths**: 
  - SOTA zero-shot multilingual + cross-lingual voice cloning
  - 9 languages + 18+ Chinese dialects
  - Bi-directional streaming (150ms latency)
  - vLLM/TensorRT-LLM deployment support
  - Active development with strong Alibaba backing
- **Key Weakness**: Requires ttsfrd package for best Chinese text normalization

### 2. GPT-SoVITS
- **Repo**: [RVC-Boss/GPT-SoVITS](https://github.com/RVC-Boss/GPT-SoVITS)
- **Stars**: 58,404 (highest in category)
- **Architecture**: GPT + SoVITS dual model
- **Key Strengths**:
  - Most popular open-source voice cloning TTS
  - Excellent few-shot quality (1 min of data)
  - WebUI with integrated tools (voice separation, ASR, dataset prep)
  - MIT license (most permissive)
  - Active community and development
- **Key Weakness**: Only 5 languages; needs fine-tuning for best voice cloning

### 3. Fish Speech S2 Pro
- **Repo**: [fishaudio/fish-speech](https://github.com/fishaudio/fish-speech)
- **Stars**: 30,690
- **Architecture**: Dual-AR (Slow AR 4.56B + Fast AR 400M)
- **Key Strengths**:
  - Best benchmark scores (lowest WER on Seed-TTS eval: 0.54% zh, 0.99% en)
  - 80+ languages, 15,000+ emotion/prosody tags
  - Native multi-speaker + multi-turn conversation generation
  - SGLang/vLLM acceleration
- **Key Weakness**: Fish Audio Research License (restrictive commercial use); very large model

### 4. ChatTTS
- **Repo**: [2noise/ChatTTS](https://github.com/2noise/ChatTTS)
- **Stars**: 39,395
- **Architecture**: Autoregressive + DVAE, optimized for dialogue
- **Key Strengths**:
  - Best prosody/naturalness for conversational speech
  - Fine-grained control (laughter, pauses, breaks)
  - Lightweight and easy to use
  - Active community with many forks/tools
- **Key Weakness**: Only zh/en; no voice cloning; non-commercial model license (CC BY-NC 4.0)

### 5. MaskGCT / Amphion
- **Repo**: [open-mmlab/Amphion](https://github.com/open-mmlab/Amphion)
- **Stars**: 9,838
- **Architecture**: Non-autoregressive masked generative codec transformer
- **Key Strengths**:
  - Comprehensive audio generation toolkit (TTS, VC, SVC, music, etc.)
  - MaskGCT: State-of-the-art zero-shot TTS, ICLR 2025 accepted
  - Vevo/Vevo2: Unified speech + singing voice generation
  - MIT license, active research from OpenMMLab
- **Key Weakness**: Complex multi-model toolkit; less plug-and-play than others

### 6. Seed-TTS (字节跳动)
- **Status**: ⚠️ **NOT OPEN SOURCE**
- Only [seed-tts-eval](https://github.com/BytedanceSpeech/seed-tts-eval) (evaluation toolkit, 1,562 stars) is public
- Available commercially via Volcengine/Doubao API
- Reported performance: strong zero-shot across zh/en

### 7. Bark (Suno AI)
- **Repo**: [suno-ai/bark](https://github.com/suno-ai/bark)
- **Stars**: 39,147
- **Architecture**: GPT-style fully generative text-to-audio
- **Key Strengths**:
  - Generates non-speech sounds, music, sound effects
  - 13 languages, 100+ voice presets
  - MIT license
- **Key Weakness**: ⚠️ **NO LONGER MAINTAINED** (last commit Apr 2024); no voice cloning; high VRAM (12GB); slow inference

### 8. XTTS v2 (Coqui TTS)
- **Repo**: [coqui-ai/TTS](https://github.com/coqui-ai/TTS)
- **Stars**: 45,498
- **Architecture**: VITS-based with GPT-2 style autoregressive + vocoder
- **Key Strengths**:
  - 16 languages, zero-shot voice cloning with 3-10s reference
  - Streaming support (<200ms latency)
  - Fine-tuning code available
- **Key Weakness**: ⚠️ **Coqui shut down, NO LONGER MAINTAINED** (last release Dec 2023); model weights have non-commercial license restrictions; TTS toolkit still maintained by community fork (tplantinga)

---

## Active vs Inactive

| Status | Tools |
|---|---|
| ✅ **Actively Maintained (2025-2026)** | CosyVoice, GPT-SoVITS, Fish Speech, ChatTTS, MaskGCT/Amphion |
| ⚠️ **Inactive / Abandoned** | Bark (last commit Apr 2024), XTTS v2/Coqui TTS (company shut down), Seed-TTS (never open-sourced) |

## License Comparison

| License Type | Tools |
|---|---|
| **Permissive (MIT/Apache 2.0)** | CosyVoice (Apache 2.0), GPT-SoVITS (MIT), MaskGCT/Amphion (MIT), Bark (MIT) |
| **Restrictive / Non-Commercial** | ChatTTS (CC BY-NC 4.0 model), Fish Speech (custom research license), XTTS v2 (Coqui model license) |
| **Closed Source** | Seed-TTS |

*Note: VoxCPM2 is from BAAI/Zhipu AI. Verify its specific license for your use case.*
