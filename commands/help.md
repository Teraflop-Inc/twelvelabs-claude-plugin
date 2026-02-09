---
name: help
description: Show TwelveLabs plugin help documentation
disable-model-invocation: true
---

# TwelveLabs Plugin Help

Welcome to the TwelveLabs Claude Code plugin! This plugin enables video AI capabilities including indexing, searching, and analyzing videos using TwelveLabs' multimodal AI platform.

## Quick Start

### 1. Index a Video

```
/twelvelabs:index /path/to/video.mp4
```
or from a URL:
```
/twelvelabs:index https://example.com/video.mp4
```

### 2. Check Indexing Status

```
/twelvelabs:status
```

### 3. Search or Analyze

Once indexed, search your videos:
```
/twelvelabs:search "person walking in the park"
```

Or analyze for insights:
```
/twelvelabs:analyze <video-id>
```

---

## Commands

| Command | Description |
|---------|-------------|
| `/twelvelabs:help` | Show this help documentation |
| `/twelvelabs:index` | Index a local video file or URL for AI analysis |
| `/twelvelabs:status` | Check the status of video indexing tasks |
| `/twelvelabs:list` | List your indexed videos and indexes |
| `/twelvelabs:search` | Search videos using natural language queries |
| `/twelvelabs:analyze` | Analyze video content to extract insights |

### `/twelvelabs:help`

Displays this help documentation with command reference and quick start guide.

**Usage:**
```
/twelvelabs:help
```

### `/twelvelabs:index`

Index a video file or URL for AI analysis. Supports local files and remote URLs including Google Drive links.

**Usage:**
```
/twelvelabs:index <path-or-url>
```

**Supported formats:** MP4, MOV, AVI, MKV, WebM

**Examples:**
```
/twelvelabs:index ./video.mp4
/twelvelabs:index /absolute/path/to/video.mp4
/twelvelabs:index https://example.com/video.mp4
/twelvelabs:index https://drive.google.com/file/d/FILE_ID/view
```

**Note:** Video indexing is asynchronous. Use `/twelvelabs:status` to check progress.

### `/twelvelabs:status`

Check the status of video indexing tasks.

**Usage:**
```
/twelvelabs:status           # Show all pending tasks
/twelvelabs:status <task-id> # Show specific task status
```

**Status values:**
- `Validating` - Video is being validated
- `Pending` - Waiting for processing
- `Queued` - Processing scheduled
- `Indexing` - Video is being indexed
- `Ready` - Indexing complete, video is searchable
- `Failed` - Indexing failed

### `/twelvelabs:list`

List your indexed videos or available indexes.

**Usage:**
```
/twelvelabs:list           # List videos in default index
/twelvelabs:list indexes   # List all available indexes
```

### `/twelvelabs:search`

Search indexed videos using natural language descriptions. Finds matching content based on visual elements, actions, sounds, and on-screen text.

**Usage:**
```
/twelvelabs:search <query>
```

**Examples:**
```
/twelvelabs:search "person giving a presentation"
/twelvelabs:search "red car driving on highway"
/twelvelabs:search "someone laughing"
```

**Returns:** Matching video segments with start/end timestamps, video filename, and stream URL.

### `/twelvelabs:analyze`

Analyze video content to extract insights.

**Usage:**
```
/twelvelabs:analyze [video-id] [prompt]
```

**Examples:**
```
/twelvelabs:analyze                                    # Analyze with video selection
/twelvelabs:analyze abc123                             # Analyze specific video
/twelvelabs:analyze abc123 "List all products mentioned"
/twelvelabs:analyze abc123 "What are the main topics?"
```

---

## Troubleshooting

### "Video too short" error

TwelveLabs requires videos to be at least 4 seconds long. Check your video duration before indexing.

### Indexing stuck in "Pending" or "Queued"

Video indexing can take several minutes depending on video length and server load. Use `/twelvelabs:status` to monitor progress. For long videos, indexing may take longer.

### "Video format not supported"

Ensure your video is in a supported format: MP4, MOV, AVI, MKV, or WebM. If using another format, convert it first:
```bash
ffmpeg -i input.video -c copy output.mp4
```

### Search returns no results

- Ensure the video is fully indexed (status: Ready)
- Try broader or different search terms
- Check that you're searching in the correct index

### Analysis fails

- Verify the video ID is correct (use `/twelvelabs:list` to find video IDs)
- Ensure the video is fully indexed

---

## Documentation

- [TwelveLabs Documentation](https://docs.twelvelabs.io/)
- [TwelveLabs API Reference](https://docs.twelvelabs.io/reference/api-reference)
- [TwelveLabs Playground](https://playground.twelvelabs.io/)
- [Video Indexing Guide](https://docs.twelvelabs.io/docs/create-indexes)
- [Search Guide](https://docs.twelvelabs.io/docs/search-overview)
- [Generate Text Guide](https://docs.twelvelabs.io/docs/generate-text-from-video)

---

## Support

For issues with this plugin, check the troubleshooting section above or report issues on the plugin repository.

For TwelveLabs platform questions, visit [TwelveLabs Support](https://twelvelabs.io/support).
