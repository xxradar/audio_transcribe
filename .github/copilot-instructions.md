# Audio Transcribe

Audio transcribe is a Python CLI application that transcribes audio files using OpenAI's Whisper API and optionally summarizes them using GPT models. The application automatically splits long audio files into 5-minute chunks to handle OpenAI API limits.

**ALWAYS reference these instructions first and fallback to search or bash commands only when you encounter unexpected information that does not match the info here.**

## Working Effectively

### Prerequisites and Setup
- Python 3.12+ is required
- Install system dependencies:
  - `sudo apt update && sudo apt install -y ffmpeg` -- NEVER CANCEL: takes 2-3 minutes
- Install Python dependencies:
  - `pip3 install openai pydub` -- takes 30-60 seconds
- Set required environment variable:
  - `export OPENAI_API_KEY='your-api-key-here'`
  - The script will exit with error message if this is not set

### Running the Application
- Basic transcription: `python3 audio.py path/to/audio.wav`
- With summarization: `python3 audio.py path/to/audio.wav --summarise`
- Custom chunk size: `python3 audio.py path/to/audio.wav --chunk-minutes 3`
- Help: `python3 audio.py --help`

### Validation Scenarios
**ALWAYS test these scenarios after making changes:**

1. **Help functionality**:
   ```bash
   python3 audio.py --help
   ```
   Should display usage information without errors.

2. **Input validation**:
   ```bash
   python3 audio.py nonexistent.wav
   ```
   Should exit with "File not found: nonexistent.wav"

3. **API key validation**:
   ```bash
   unset OPENAI_API_KEY
   echo "test" > dummy.wav
   python3 audio.py dummy.wav
   ```
   Should exit with "Set the OPENAI_API_KEY environment variable first."

4. **Audio processing test** (without API calls):
   ```bash
   # Create test audio file
   ffmpeg -f lavfi -i "sine=frequency=1000:duration=10" -acodec pcm_s16le test_short.wav
   
   # Test short audio (no chunking)
   export OPENAI_API_KEY="dummy-key-for-testing"
   timeout 5 python3 audio.py test_short.wav 2>&1
   ```
   Should show "Audio duration (10.0s) is within limit, no chunking needed" and fail at API call.

5. **Chunking functionality test**:
   ```bash
   # Create longer test audio file
   ffmpeg -f lavfi -i "sine=frequency=1000:duration=400" -acodec pcm_s16le test_long.wav
   
   # Test chunking with 2-minute chunks
   export OPENAI_API_KEY="dummy-key-for-testing"
   timeout 10 python3 audio.py test_long.wav --chunk-minutes 2 2>&1
   ```
   Should show creation of 4 chunks and fail at API call.

6. **Clean up test files**:
   ```bash
   rm -f test_short.wav test_long.wav transcript.txt summary.md
   ```

## Timing Expectations
- **Dependency installation**: 2-3 minutes for ffmpeg, 30-60 seconds for Python packages
- **Audio file loading and chunking**: Varies by file size, typically 1-10 seconds for most files
- **API calls**: Depends on OpenAI API response times, typically 5-30 seconds per chunk
- **NEVER CANCEL**: All operations are relatively fast (under 5 minutes total for typical use)

## Key Information
- **Supported audio formats**: WAV, MP3, FLAC, M4A (anything ffmpeg can process)
- **Output files**: 
  - `transcript.txt` - Raw transcription text
  - `summary.md` - Summary and action items (only with --summarise flag)
- **Chunking**: Automatically splits files longer than specified duration (default 5 minutes)
- **Models used**:
  - Transcription: `whisper-1` (OpenAI's Whisper model)
  - Summarization: `gpt-4o-mini` (configurable in code)

## Common Tasks and Troubleshooting

### Repository Structure
```
audio_transcribe/
├── audio.py          # Main application script
└── .github/
    └── copilot-instructions.md
```

### Dependencies
- **openai** (1.99.6+): OpenAI API client
- **pydub** (0.25.1+): Audio file processing
- **ffmpeg**: System dependency for audio format conversion

### Environment Variables
- **OPENAI_API_KEY**: Required for API access to OpenAI Whisper and GPT models

### Error Handling
The application has robust error handling for:
- Missing files
- Missing API keys
- API errors
- Audio processing errors
- Chunk processing failures

### Making Changes
When modifying the code:
1. **Always test** all validation scenarios listed above
2. **Verify** that error messages are clear and helpful
3. **Test** with both short and long audio files
4. **Ensure** chunking logic works correctly
5. **Check** that output files are created properly
6. **Validate syntax**: `python3 -m py_compile audio.py`

### Testing with Real Audio
To test with actual transcription (requires valid API key):
1. Get a real audio file (WAV, MP3, etc.)
2. Set valid OPENAI_API_KEY
3. Run: `python3 audio.py your_audio_file.wav`
4. Verify `transcript.txt` is created with actual transcription
5. Test summarization: `python3 audio.py your_audio_file.wav --summarise`
6. Verify `summary.md` is created with summary and action items

### File Output Locations
- Output files are created in the current working directory
- `transcript.txt`: Contains the raw transcription text
- `summary.md`: Contains formatted summary and action items (only when --summarise is used)
- Temporary chunk files are created in `/tmp/audio_chunks_*` and automatically cleaned up

### Performance Considerations
- Large files are automatically chunked to avoid API limits
- Each chunk is processed sequentially
- Temporary files are cleaned up automatically
- Memory usage is minimal as audio is processed in chunks