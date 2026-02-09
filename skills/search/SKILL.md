---
name: search
description: Search indexed videos using natural language. Use when the user wants to find specific content, moments, or scenes in their indexed videos. Triggers on phrases like "find the part where...", "search for...", "look for...", "where does...", "when does...".
---

# Search Videos Using Natural Language

Search indexed videos using natural language descriptions. TwelveLabs interprets your query to find matching content based on visual elements, actions, sounds, and on-screen text.

## When to Use This Skill

Use this skill when the user:
- Asks to find something in a video ("find the part where someone laughs")
- Wants to search for content ("search for a red car")
- Asks about when/where something happens ("when does the presenter mention AI?")
- Describes a scene they want to locate ("look for the sunset scene")

## Instructions

### Step 1: Extract the Search Query

Identify what the user wants to find from their natural language request. Convert their question into a search query.

**Examples**:
- "Find the part where someone is walking" → query: "a person walking"
- "When does the presenter talk about AI?" → query: "presenter talking about AI"
- "Look for any red cars in my videos" → query: "red car"

### Step 2: Call the Search MCP Tool

Use the `mcp__twelvelabs-mcp__search` tool:

```
Tool: mcp__twelvelabs-mcp__search
Parameters:
  query: "<extracted search query>"
```

**Note**: The search is performed across all videos in the default index. To search a specific index, provide the optional `indexId` parameter.

### Step 3: Display Search Results

Format the search results clearly for the user. The search returns matching segments with timestamps.

**For each video with matching segments, display**:
- Filename of the video (if available)
- URL of the video stream (if available)
- Bulleted list of start and end times for matching segments

**Example output with results**:
```
Found matches for: "a person walking"

**video_filename.mp4**
Stream: https://stream.url/video.m3u8

Matching segments:
- 00:12 - 00:28 (16 seconds)
- 01:45 - 02:03 (18 seconds)
- 03:30 - 03:45 (15 seconds)

**another_video.mp4**
Stream: https://stream.url/another.m3u8

Matching segments:
- 00:05 - 00:15 (10 seconds)

Found 4 matching segments across 2 videos.
```

**Example output with no results**:
```
No matching content found for: "a person walking"

Tips:
- Try different keywords or descriptions
- Use broader search terms
- Make sure your videos are fully indexed
```

### Step 4: Format Timestamps

Convert timestamps to human-readable format:
- For timestamps under 1 hour: `MM:SS` (e.g., `01:45`)
- For timestamps 1 hour or more: `HH:MM:SS` (e.g., `1:23:45`)

Calculate segment duration as `end_time - start_time` and display in parentheses.

## How TwelveLabs Search Works

TwelveLabs uses multimodal AI to understand video content:
- **Visual**: Objects, scenes, colors, people, actions
- **Audio**: Speech, music, sounds
- **Text**: On-screen text, captions, titles

The search interprets natural language queries and finds segments that match across all these modalities.

## Important Notes

- **Indexed Videos Only**: Search only works on videos that have been fully indexed
- **Default Index**: Searches the default index unless specified otherwise
- **Segment Precision**: Results show specific time ranges where content matches
