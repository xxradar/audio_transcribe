# Audio Transcribe

A powerful Python tool that transcribes audio files using OpenAI's Whisper API and optionally summarizes the content using GPT models. Designed to handle long audio files by automatically splitting them into manageable chunks.

## ✨ Features

- **🎯 Accurate Transcription**: Leverages OpenAI's Whisper-1 model for high-quality speech-to-text conversion
- **📝 AI-Powered Summarization**: Optional GPT-4o-mini powered summaries with action item extraction
- **⚡ Smart Chunking**: Automatically splits long audio files into 5-minute segments to avoid API limits
- **🎵 Multi-Format Support**: Works with WAV, MP3, FLAC, M4A, and other common audio formats
- **🧹 Clean Processing**: Automatic cleanup of temporary files after processing
- **⚙️ Configurable**: Customizable chunk duration and model selection
- **💪 Robust Error Handling**: Graceful handling of API errors and file processing issues

## 🚀 How It Works

The tool follows a simple but effective workflow:

1. **Audio Analysis**: Loads the input audio file and checks its duration
2. **Smart Chunking**: If the audio is longer than the configured limit (default: 5 minutes), it automatically splits the file into smaller chunks to stay within OpenAI API limits
3. **Transcription**: Each chunk is sent to OpenAI's Whisper API for transcription
4. **Combination**: All chunk transcripts are seamlessly combined into a single coherent text
5. **Summarization** (optional): The complete transcript is analyzed by GPT to produce an executive summary with action items
6. **Output**: Results are saved as text files with automatic cleanup of temporary files

This approach ensures reliable processing of audio files of any length while maintaining transcription quality and API compliance.

## 📋 Requirements

- Python 3.7+
- OpenAI API key
- Required Python packages:
  - `openai` - OpenAI Python SDK
  - `pydub` - Audio processing library

## 🛠️ Installation

1. Clone this repository:
   ```bash
   git clone https://github.com/xxradar/audio_transcribe.git
   cd audio_transcribe
   ```

2. Install required dependencies:
   ```bash
   pip install openai pydub
   ```

3. Set up your OpenAI API key:
   ```bash
   export OPENAI_API_KEY='your-api-key-here'
   ```

## 📖 Usage

### Basic Transcription

Transcribe an audio file to text:

```bash
python audio.py path/to/your/audio.wav
```

This will create a `transcript.txt` file with the full transcription.

### Transcription with Summary

Transcribe and generate an AI summary with action items:

```bash
python audio.py path/to/your/audio.wav --summarise
```

This creates both `transcript.txt` and `summary.md` files.

### Custom Chunk Duration

For very long files, you can adjust the chunk size:

```bash
python audio.py path/to/your/audio.wav --chunk-minutes 10 --summarise
```

### Command-Line Options

```
python audio.py [-h] [--summarise] [--chunk-minutes CHUNK_MINUTES] audio_file

Arguments:
  audio_file            Path to WAV/MP3/FLAC/M4A file

Options:
  -h, --help            Show help message
  --summarise           Also produce a summary & action list
  --chunk-minutes CHUNK_MINUTES
                        Split audio into chunks of this many minutes (default: 5)
```

## ⚙️ Configuration

The tool includes several configurable parameters at the top of `audio.py`:

```python
MODEL_TRANSCRIBE = "whisper-1"     # Whisper model for transcription
MODEL_SUMMARISE = "gpt-4o-mini"    # GPT model for summarization
OUTPUT_TXT = "transcript.txt"      # Transcript output filename
OUTPUT_MD = "summary.md"           # Summary output filename
MAX_TOKEN_SUMMARY = 1024           # Maximum tokens for summary
CHUNK_DURATION_MS = 5 * 60 * 1000  # Default chunk duration (5 minutes)
```

## 📄 Output Files

### transcript.txt
Contains the complete transcription in plain text format. Multiple chunks are seamlessly combined with proper spacing.

### summary.md (when using --summarise)
Contains an executive-style summary including:
- Concise overview (≈150 words)
- Deduplicated action items with responsible parties and deadlines
- Markdown formatting for easy reading

## 💡 Use Cases

- **Meeting Transcription**: Convert recorded meetings into searchable text with action items
- **Interview Processing**: Transcribe interviews for analysis and documentation
- **Lecture Notes**: Convert educational content into readable format
- **Podcast Processing**: Generate transcripts and summaries for podcast episodes
- **Research Interviews**: Process qualitative research recordings

## 🔧 Technical Details

- **API Compliance**: Automatically handles OpenAI's file size limits through intelligent chunking
- **Memory Efficient**: Processes large files without loading everything into memory
- **Format Flexibility**: Uses pydub for broad audio format compatibility
- **Temporary File Management**: Creates and cleans up temporary chunks automatically
- **Error Recovery**: Continues processing even if individual chunks fail

## 📝 Example Output

**Transcript excerpt:**
```
This is the beginning of our weekly team meeting. We have several important topics to discuss today including the product roadmap and budget planning...
```

**Summary excerpt:**
```markdown
## Summary
The team discussed Q4 product roadmap priorities, focusing on user authentication improvements and mobile app development. Budget allocation was reviewed with emphasis on engineering resources.

## Action Items
• John → Complete user auth design mockups → December 15th
• Sarah → Review mobile development budget → End of week
• Team → Finalize Q4 milestone definitions → Next meeting
```

---

*This tool was created to streamline audio processing workflows using OpenAI's powerful AI models.*