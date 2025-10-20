# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an Android application that demonstrates audio recording and WAV file creation. The project provides two implementations of WAV file builders - one in Java (`WavFileBuilderJava`) and one in Kotlin (`WavFileBuilderKotlin`) - that create properly formatted WAV file headers and combine them with raw audio data.

## Architecture

### Core Components

- **WavFileBuilderJava** (`app/src/main/java/com/theeasiestway/wavformat/WavFileBuilderJava.java`): Java implementation of WAV file header builder with fluent interface
- **WavFileBuilderKotlin** (`app/src/main/java/com/theeasiestway/wavformat/WavFileBuilderKotlin.kt`): Kotlin implementation with identical functionality
- **VoiceRecorder** (`app/src/main/java/com/theeasiestway/wavformat/VoiceRecorder.kt`): Handles audio recording using Android's AudioRecord API
- **MainActivity** (`app/src/main/java/com/theeasiestway/wavformat/MainActivity.kt`): UI controller that orchestrates recording and file creation

### WAV File Structure

The WAV builders construct proper WAV file headers according to the RIFF specification:
- **Chunk ID**: "RIFF" (bytes 0-3)
- **Format**: "WAVE" (bytes 8-11)
- **Subchunk1ID**: "fmt " (bytes 12-15)
- **Subchunk2ID**: "data" (bytes 36-39)

Key header fields include:
- Audio format (PCM = 1)
- Number of channels (mono/stereo)
- Sample rate
- Bits per sample
- Byte rate
- Block align
- Data size

## Development Commands

### Building the Project
```bash
# Build the APK
./gradlew assembleDebug

# Build and install to connected device
./gradlew installDebug

# Clean build
./gradlew clean
```

### Running the Application
```bash
# Run on connected device/emulator
./gradlew installDebug && adb shell am start -n com.theeasiestway.wavformat/.MainActivity
```

## Key Implementation Details

### Audio Recording Configuration
- Uses `AudioRecord` with `MediaRecorder.AudioSource.MIC`
- Default sample rate: 44100 Hz
- Audio format: 16-bit PCM, mono channel
- Buffer size calculated using `AudioRecord.getMinBufferSize()`

### WAV Builder Usage Pattern
```kotlin
val wavFile = WavFileBuilderKotlin()
    .setAudioFormat(WavFileBuilderKotlin.PCM_AUDIO_FORMAT)
    .setSampleRate(44100)
    .setBitsPerSample(WavFileBuilderKotlin.BITS_PER_SAMPLE_16)
    .setNumChannels(WavFileBuilderKotlin.CHANNELS_MONO)
    .setSubChunk1Size(WavFileBuilderKotlin.SUBCHUNK_1_SIZE_PCM)
    .build(audioData)
```

### Permissions Required
- `RECORD_AUDIO` - for audio recording
- `WRITE_EXTERNAL_STORAGE` - for saving WAV files
- `READ_EXTERNAL_STORAGE` - for file operations

## Project Structure

```
app/
├── src/main/java/com/theeasiestway/wavformat/
│   ├── WavFileBuilderJava.java    # Java WAV builder implementation
│   ├── WavFileBuilderKotlin.kt    # Kotlin WAV builder implementation
│   ├── VoiceRecorder.kt           # Audio recording logic
│   └── MainActivity.kt            # Main UI and orchestration
```

## Notes

- The project demonstrates both Java and Kotlin implementations for educational purposes
- No unit tests are currently implemented
- Uses older Android support libraries (v7 appcompat, API 28)
- Files are saved to the Downloads directory by default