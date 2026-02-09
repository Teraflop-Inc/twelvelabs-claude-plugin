---
name: analyze
description: Analyze indexed videos to understand their content. Use when user asks "what is this video about?", "summarize the video", "analyze this video", or has questions about video content.
---

# Analyze Video Content

Analyze indexed videos using TwelveLabs AI to understand and extract information from video content.

## When to Use This Skill

Use this skill when the user:
- Asks what a video is about ("what is this video about?")
- Wants to analyze video content ("analyze this video")
- Has questions about a video ("what happens in this video?")
- Wants information extracted from video content

## Instructions

### Step 1: Get Video ID (if not known)

If the user hasn't specified a video or video ID:

1. Call `mcp__twelvelabs-mcp__list-videos` to get available videos
2. Display the list to the user:
   ```
   Which video would you like to analyze?

   | # | Video ID | Filename |
   |---|----------|----------|
   | 1 | abc123   | demo.mp4 |
   | 2 | def456   | tutorial.mp4 |
   ```
3. Ask the user to select a video

### Step 2: Call the Analyze Video MCP Tool

Use the `mcp__twelvelabs-mcp__analyse-video` tool with the user's question as the prompt:

```
Tool: mcp__twelvelabs-mcp__analyse-video
Parameters:
  videoId: "<video-id>"
  type: "open-ended"
  prompt: "<user's question or 'Provide a comprehensive summary of this video'>"
```

If the user just says "analyze" without a specific question, use a general prompt like "Provide a comprehensive summary of this video including key moments and topics covered."

### Step 3: Display Results

Format the analysis results clearly:

```
Video Analysis

<analysis output>

Video: <filename or video-id>
```

### Step 4: Handle Errors

**Video not found**:
```
Video Not Found

Could not find video with ID: <video-id>

Use "what videos do I have?" to see available videos, or index a new video first.
```

**Analysis failed**:
```
Analysis Failed

The analysis could not be completed. This might happen if:
- The video is still being indexed (ask "is my video ready?")
- The index doesn't support generative models

Error: <error message if available>
```

## Example Interactions

**User**: "What is this video about?"
**Action**: List videos → user selects → analyze with prompt "What is this video about?"

**User**: "Analyze video abc123"
**Action**: Analyze with general summary prompt

**User**: "What products are mentioned in the demo video?"
**Action**: Analyze with prompt "What products are mentioned in this video?"

## Important Notes

- **Indexed Videos Only**: Analysis only works on videos that have been fully indexed
- **Generative Model Required**: The index must include the `generative` model for analysis
