---
name: index
description: Index video files or URLs for AI search and analysis. Use when user wants to add a video for processing, says "index this video", "add this video for analysis", or mentions a video file they want to work with.
---

# Index Videos for AI Analysis

Index local video files or remote URLs for AI search and analysis using TwelveLabs. Once indexed, videos can be searched with natural language and analyzed to generate summaries, chapters, highlights, and more.

## When to Use This Skill

Use this skill when the user:
- Explicitly asks to index a video ("index this video", "add this video")
- Wants to enable AI search on a video ("I want to search this video")
- Mentions a video file/URL they want to work with ("process this video file")
- Provides a video path or URL in context of wanting to analyze it

## Instructions

### Step 1: Identify the Video Source

Extract the video path or URL from the user's message:

- **Local file path**: `/path/to/video.mp4`, `./video.mp4`, `~/Videos/demo.mov`
- **Remote URL**: `https://example.com/video.mp4`
- **Google Drive link**: `https://drive.google.com/file/d/FILE_ID/view`

If no path/URL is provided, ask the user:
```
Please provide the video file path or URL you'd like to index.

Examples:
- Local file: /path/to/video.mp4 or ./video.mp4
- Remote URL: https://example.com/video.mp4
- Google Drive: https://drive.google.com/file/d/FILE_ID/view
```

### Step 2: Validate the Input

**For Local Files**:
1. Resolve relative paths to absolute paths
2. Verify the file exists
3. Check file extension is supported: `.mp4`, `.mov`, `.avi`, `.mkv`, `.webm`

**Validation errors**:
- File not found: "Video file not found at: `<path>`. Please check the path and try again."
- Unsupported format: "Unsupported video format: `<extension>`. Supported formats: mp4, mov, avi, mkv, webm"

**For URLs**:
1. Verify URL starts with `http://` or `https://`
2. Note if it's a Google Drive link (special handling)

### Step 3: Start Video Indexing

Use the `mcp__twelvelabs-mcp__start-video-indexing-task` tool:

**For Local Files**:
```
Tool: mcp__twelvelabs-mcp__start-video-indexing-task
Parameters:
  videoFilePath: "<absolute-path-to-video>"
```

**For URLs (including Google Drive)**:
```
Tool: mcp__twelvelabs-mcp__start-video-indexing-task
Parameters:
  videoUrl: "<url>"
```

**Google Drive Notes**:
- Single file links index that specific file
- Folder links index all MP4 videos in the folder (multiple tasks started)

### Step 4: Report Result

**On success (single video)**:
```
Video indexing started!

Source: <filename-or-url>
Task ID: <task_id>

Indexing runs in the background and may take several minutes depending on video length.
Ask "is my video ready?" to check the indexing progress.
```

**On success (Google Drive folder)**:
```
Video indexing started for Google Drive folder!

Multiple indexing tasks started:
- Task ID: <task_id_1> - <filename_1>
- Task ID: <task_id_2> - <filename_2>

Ask "is my video ready?" to check indexing progress for all tasks.
```

**On failure**:
Report the error message from the API with helpful context.

## Example Interactions

**User**: "Index this video: /Users/me/Videos/demo.mp4"
**Action**: Validate file exists → call start-video-indexing-task with videoFilePath

**User**: "I want to search this video https://example.com/video.mp4"
**Action**: Call start-video-indexing-task with videoUrl

**User**: "Add this Google Drive folder for analysis: https://drive.google.com/drive/folders/ABC123"
**Action**: Call start-video-indexing-task with videoUrl (handles folder automatically)

**User**: "Process my meeting recording"
**Action**: Ask for the file path or URL

## Important Notes

- **Minimum Duration**: Videos must be at least 4 seconds long
- **Async Processing**: Indexing runs in the background on TwelveLabs servers
- **Processing Time**: Indexing can take several minutes depending on video length
- **Status Checking**: User can ask "is my video ready?" to check progress
- **File Access**: Local files must be accessible to the TwelveLabs MCP server
