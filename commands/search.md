---
name: search
description: Search indexed videos using natural language queries
disable-model-invocation: true
argument-hint: [query]
---

# /twelvelabs:search - Search Videos Using Natural Language

Search your indexed videos using natural language descriptions. TwelveLabs interprets your query to find matching content based on visual elements, actions, sounds, and on-screen text.

## Usage

```
/twelvelabs:search <query>
```

**User provided:** `$ARGUMENTS`

## Arguments

- `query` - **Required**. Natural language description of what you're looking for

## Examples

```
/twelvelabs:search a person walking through a forest
/twelvelabs:search red car driving fast
/twelvelabs:search someone giving a presentation
/twelvelabs:search sunset over the ocean
/twelvelabs:search text showing "hello world"
```

## Instructions for Claude

When the user invokes `/twelvelabs:search`, the query is provided in `$ARGUMENTS`. Follow these steps:

### Step 1: Validate Query

Check that a query string was provided in `$ARGUMENTS`:

- If `$ARGUMENTS` is empty or not provided: Ask the user what they want to search for
- If query provided: Proceed to search

**Example validation error**:
```
❌ Search Query Required

Please provide a natural language description of what you're looking for.

Usage: /twelvelabs:search <query>
Example: /twelvelabs:search a person walking
```

### Step 2: Call the Search MCP Tool

Use the `mcp__twelvelabs-mcp__search` tool with the user's query:

```
Tool: mcp__twelvelabs-mcp__search
Parameters:
  query: "<user's search query from $ARGUMENTS>"
```

**Note**: The search is performed across all videos in the default index. To search a specific index, you can provide the optional `indexId` parameter:
```
Tool: mcp__twelvelabs-mcp__search
Parameters:
  query: "<user's search query>"
  indexId: "<specific-index-id>"
```

### Step 3: Display Search Results

Format the search results clearly for the user. The search returns matching segments with timestamps.

**For each video with matching segments, display**:
- Filename of the video (if available)
- URL of the video stream (if available)
- Bulleted list of start and end times for matching segments

**Example output with results**:
```
🔍 Search Results for: "a person walking"

📹 **video_filename.mp4**
   Stream: https://stream.url/video.m3u8

   Matching segments:
   • 00:12 - 00:28 (16 seconds)
   • 01:45 - 02:03 (18 seconds)
   • 03:30 - 03:45 (15 seconds)

📹 **another_video.mp4**
   Stream: https://stream.url/another.m3u8

   Matching segments:
   • 00:05 - 00:15 (10 seconds)

Found 4 matching segments across 2 videos.
```

**Example output with no results**:
```
🔍 No Results Found

No matching content found for: "a person walking"

Tips:
• Try different keywords or descriptions
• Use broader search terms
• Make sure your videos are fully indexed (check with /twelvelabs:status)
```

### Step 4: Format Timestamps

Convert timestamps to human-readable format:
- For timestamps under 1 hour: `MM:SS` (e.g., `01:45`)
- For timestamps 1 hour or more: `HH:MM:SS` (e.g., `1:23:45`)

Calculate segment duration as `end_time - start_time` and display in parentheses.

## Search Tips for Users

- **Visual elements**: Describe objects, people, colors, actions visible in the video
- **Audio/speech**: Describe sounds, music, or spoken words
- **On-screen text**: Search for text that appears in the video
- **Actions**: Describe activities or movements
- **Scenes**: Describe environments, settings, or locations

## How TwelveLabs Search Works

TwelveLabs uses multimodal AI to understand video content:
- **Visual**: Objects, scenes, colors, people, actions
- **Audio**: Speech, music, sounds
- **Text**: On-screen text, captions, titles

The search interprets your natural language query and finds segments that match across all these modalities.

## Important Notes

- **Indexed Videos Only**: Search only works on videos that have been fully indexed
- **Default Index**: Searches the default index unless specified otherwise
- **Segment Precision**: Results show specific time ranges where content matches
- **Confidence Scores**: Some results may include confidence scores - higher is better

## Related Commands

- `/twelvelabs:list` - List all indexed videos
- `/twelvelabs:index` - Index a new video
- `/twelvelabs:status` - Check if videos are ready for search
- `/twelvelabs:analyze` - Get detailed analysis of specific video content
