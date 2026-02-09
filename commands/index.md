---
name: index
description: Index a video file or URL for AI analysis using TwelveLabs
disable-model-invocation: true
argument-hint: [path-or-url]
---

# /twelvelabs:index - Index Videos for AI Analysis

Index local video files or remote URLs for AI analysis using TwelveLabs.

## Usage

```
/twelvelabs:index <path-or-url>
```

**User provided:** `$ARGUMENTS`

## Arguments

- `path-or-url` - **Required**. Either:
  - Local file path (absolute or relative) to a video file
  - Remote URL (http:// or https://) to a video
  - Google Drive link (file or public folder)

## Supported Video Formats

- `.mp4` - MPEG-4 Video
- `.mov` - QuickTime Movie
- `.avi` - Audio Video Interleave
- `.mkv` - Matroska Video
- `.webm` - WebM Video

## Examples

### Local Files
```
/twelvelabs:index ./video.mp4
/twelvelabs:index /absolute/path/to/video.mp4
/twelvelabs:index ../videos/meeting-recording.mov
```

### Remote URLs
```
/twelvelabs:index https://example.com/video.mp4
/twelvelabs:index https://storage.googleapis.com/bucket/video.mp4
```

### Google Drive
```
/twelvelabs:index https://drive.google.com/file/d/FILE_ID/view
/twelvelabs:index https://drive.google.com/drive/folders/FOLDER_ID
```

## Instructions for Claude

When the user invokes `/twelvelabs:index`, the argument is provided in `$ARGUMENTS`. Follow these steps:

### Step 1: Parse Arguments

Extract the path or URL from `$ARGUMENTS`. If `$ARGUMENTS` is empty or not provided:
- Display: "Please provide a path or URL. Usage: `/twelvelabs:index <path-or-url>`"
- Stop processing.

### Step 2: Determine Input Type

Determine if the input is a local file path or a URL:

- **URL**: Starts with `http://` or `https://`
- **Local file**: Everything else (absolute or relative path)

### Step 3: Validate the Input

#### For Local Files:
1. Resolve the path to an absolute path if relative
2. Verify the file exists at the specified path
3. Check that the file extension is a supported video format (mp4, mov, avi, mkv, webm)

If validation fails:
- File not found: "Video file not found at: `<path>`. Please check the path and try again."
- Unsupported format: "Unsupported video format: `<extension>`. Supported formats: mp4, mov, avi, mkv, webm"

#### For URLs:
1. Verify the URL is well-formed (starts with http:// or https://)
2. Identify if it's a Google Drive link (contains `drive.google.com`)
3. Note: The TwelveLabs API will validate if the URL is accessible

If validation fails:
- Invalid URL: "Invalid URL format. Please provide a valid http:// or https:// URL."

### Step 4: Start Video Indexing

Use the `mcp__twelvelabs-mcp__start-video-indexing-task` MCP tool:

#### For Local Files:
```
Tool: mcp__twelvelabs-mcp__start-video-indexing-task
Parameters:
  videoFilePath: "<absolute-path-to-video>"
```

#### For URLs (including Google Drive):
```
Tool: mcp__twelvelabs-mcp__start-video-indexing-task
Parameters:
  videoUrl: "<url>"
```

**Google Drive Notes:**
- Single file links (e.g., `https://drive.google.com/file/d/FILE_ID/view`) will index that file
- Folder links (e.g., `https://drive.google.com/drive/folders/FOLDER_ID`) will index all MP4 videos in the folder and start multiple tasks

### Step 5: Track Task in Config

After a successful indexing start, track the task in the local config:

```bash
python3 -c "
import sys
sys.path.insert(0, '.twelvelabs')
from config_helper import add_pending_task
add_pending_task('<task_id>', '<source_path_or_url>')
"
```

Replace `<task_id>` with the task ID from the MCP tool response and `<source_path_or_url>` with the original input.

### Step 6: Report Result

After calling the indexing tool:

1. **On success** (single video):
   ```
   Video indexing started for: <filename-or-url>

   Task ID: <task_id>

   Video indexing is asynchronous and may take several minutes depending on video length.
   Use `/twelvelabs:status` to check the indexing progress.
   ```

2. **On success** (Google Drive folder):
   ```
   Video indexing started for Google Drive folder

   Multiple indexing tasks started:
   - Task ID: <task_id_1> - <filename_1>
   - Task ID: <task_id_2> - <filename_2>
   ...

   Video indexing is asynchronous and may take several minutes.
   Use `/twelvelabs:status` to check indexing progress for all tasks.
   ```

3. **On failure**: Report the error to the user with the error message from the API.

## Important Notes

- **Minimum Duration**: Videos must be at least 4 seconds long for TwelveLabs indexing
- **Async Processing**: Video indexing is asynchronous - it runs in the background
- **Status Checking**: Use `/twelvelabs:status` to monitor indexing progress
- **File Access**: The file must be accessible to the TwelveLabs MCP server

## Related Commands

- `/twelvelabs:status` - Check indexing task status
- `/twelvelabs:list` - List indexed videos
- `/twelvelabs:search` - Search indexed videos
- `/twelvelabs:analyze` - Analyze indexed videos
