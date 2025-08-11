# Audio Transcribe

Audio Transcribe is a Python command-line tool that transcribes audio files using OpenAI's Whisper API and optionally summarizes content using GPT models. It automatically splits long audio files into manageable chunks to work within API limits.

Always reference these instructions first and fallback to search or bash commands only when you encounter unexpected information that does not match the info here.

## Working Effectively

### Bootstrap and Setup
- Install system dependencies:
  - `sudo apt-get update && sudo apt-get install -y python3 python3-pip ffmpeg`
  - FFmpeg is REQUIRED for audio processing - pydub will fail without it
- Install Python dependencies:
  - `pip3 install openai pydub`
  - Installation takes ~30 seconds. NEVER CANCEL.
- Set up OpenAI API key (REQUIRED for functionality):
  - `export OPENAI_API_KEY='sk-your-key-here'`
  - Script will immediately exit with clear error if key is missing

### Running the Application
- Basic transcription:
  - `python3 audio.py path/to/audio.wav`
  - Creates `transcript.txt` with full transcription
- Transcription with AI summary:
  - `python3 audio.py path/to/audio.wav --summarise`
  - Creates both `transcript.txt` and `summary.md`
- Custom chunk duration for long files:
  - `python3 audio.py path/to/audio.wav --chunk-minutes 10`
  - Default is 5 minutes; adjust for very long recordings

### Validation
- ALWAYS test command-line interface first:
  - `python3 audio.py --help` - should display usage without errors
  - `python3 audio.py nonexistent.wav` - should show "File not found" error
  - `python3 audio.py test.wav` (without API key) - should show "Set the OPENAI_API_KEY" error
- Test audio processing capabilities:
  - Create test audio: `ffmpeg -f lavfi -i "sine=frequency=440:duration=10" -ac 1 -ar 22050 test.wav`
  - Takes <1 second to create. NEVER CANCEL.
  - Verify chunking works with long audio (6+ minutes)
- ALWAYS verify dependencies are working:
  - `python3 -c "import openai, pydub; print('Dependencies OK')"`
  - `ffmpeg -version | head -1` - should show version info

## System Requirements and Timing

### Dependencies and Installation Times
- Python 3.7+ (tested with 3.12.3)
- `pip3 install openai pydub` - takes ~30 seconds. NEVER CANCEL.
- `sudo apt-get install ffmpeg` - takes ~2-3 minutes. NEVER CANCEL. Set timeout to 300+ seconds.
- Total setup time: ~3-4 minutes maximum

### Performance Expectations
- Audio file creation (test): <1 second for 10-second clips, <1 second for 6-minute clips
- Script startup and validation: <1 second
- Chunking 6-minute audio into 3-minute chunks: <1 second
- NO long-running build processes - this is a single-file Python script

### API Limitations and Behavior
- OpenAI API calls will fail in testing environment (no real API key)
- Chunking and file processing work without API calls
- Script handles API errors gracefully with clear error messages

## File Structure and Navigation

### Repository Layout
```
.
├── README.md          # Comprehensive documentation (5781 bytes)
├── audio.py          # Main Python script (7690 bytes)
└── .github/
    └── copilot-instructions.md  # This file
```

### Key Code Sections in audio.py
- Lines 23-30: Configuration constants (models, output files, chunk duration)
- Lines 33-72: `split_audio_into_chunks()` - handles long audio files
- Lines 75-89: `transcribe_audio()` - OpenAI Whisper API call
- Lines 91-125: `transcribe_audio_chunks()` - processes multiple chunks
- Lines 128-153: `summarise_text()` - GPT summarization
- Lines 156-195: `main()` - CLI argument parsing and workflow

### Output Files
- `transcript.txt` - Full transcription in plain text
- `summary.md` - Executive summary with action items (when using --summarise)
- Temporary chunk files are automatically cleaned up

## Common Tasks and Troubleshooting

### Testing Changes
- ALWAYS test basic functionality first:
  - `python3 audio.py --help`
  - `python3 -m py_compile audio.py` - syntax check
- Create test audio for validation:
  - `ffmpeg -f lavfi -i "sine=frequency=440:duration=10" -ac 1 -ar 22050 test.wav`
- Test error handling:
  - Missing file: `python3 audio.py nonexistent.wav`
  - Missing API key: `python3 audio.py test.wav` (without OPENAI_API_KEY)

### Code Quality
- No formal linting tools configured in repository
- Basic syntax checking: `python3 -m py_compile audio.py`
- Code follows PEP 8 style conventions
- No automated tests - manual validation required

### Supported Audio Formats
- WAV (primary format for testing)
- MP3, FLAC, M4A (via ffmpeg/pydub)
- Test with: `ffmpeg -i input.mp3 output.wav` for format conversion

### Configuration Customization
Edit lines 24-29 in audio.py:
- `MODEL_TRANSCRIBE = "whisper-1"` - Whisper model
- `MODEL_SUMMARISE = "gpt-4o-mini"` - GPT model for summaries
- `OUTPUT_TXT = "transcript.txt"` - Transcript filename
- `OUTPUT_MD = "summary.md"` - Summary filename
- `CHUNK_DURATION_MS = 5 * 60 * 1000` - Default 5-minute chunks

## Workflow for Making Changes

1. **Always start with dependency verification:**
   ```bash
   python3 --version  # Should be 3.7+
   python3 -c "import openai, pydub; print('OK')"
   ffmpeg -version | head -1
   ```

2. **Test current functionality:**
   ```bash
   python3 audio.py --help
   python3 -m py_compile audio.py
   ```

3. **Create test scenarios:**
   ```bash
   ffmpeg -f lavfi -i "sine=frequency=440:duration=10" -ac 1 -ar 22050 test.wav
   python3 audio.py test.wav  # Should fail with API key error
   ```

4. **Make minimal changes and retest immediately**

5. **Clean up test files:**
   ```bash
   rm -f test*.wav transcript.txt summary.md
   ```

## CRITICAL Reminders

- **NO BUILD PROCESS**: This is a single Python file - no compilation needed
- **NEVER CANCEL**: FFmpeg installation takes 2-3 minutes, set timeout to 300+ seconds
- **API KEY REQUIRED**: All real functionality requires OPENAI_API_KEY environment variable
- **DEPENDENCY ORDER**: Install ffmpeg BEFORE testing pydub functionality
- **TEST FIRST**: Always verify `python3 audio.py --help` works before making changes
- **CLEAN VALIDATION**: Remove test files when done to avoid repository pollution

The tool is designed to be simple and reliable - most issues are dependency-related rather than code problems.