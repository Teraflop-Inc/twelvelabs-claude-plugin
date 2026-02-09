---
name: list
description: List indexed videos or available indexes on TwelveLabs
disable-model-invocation: true
argument-hint: [indexes]
---

# /twelvelabs:list - List Indexed Videos and Indexes

List all indexed videos in your default index or view all available indexes.

## Usage

```
/twelvelabs:list [subcommand]
```

**User provided:** `$ARGUMENTS`

## Arguments

- `subcommand` - **Optional**. Either `videos` (default) or `indexes`.
  - `videos` - List all videos in the default index
  - `indexes` - List all available indexes in your TwelveLabs account

## Examples

### List Videos (default)
```
/twelvelabs:list
/twelvelabs:list videos
```

### List All Indexes
```
/twelvelabs:list indexes
```

## Instructions for Claude

When the user invokes `/twelvelabs:list`, the optional argument is provided in `$ARGUMENTS`. Follow these steps:

### Step 1: Determine Subcommand

Check what was provided in `$ARGUMENTS`:
- If `$ARGUMENTS` is empty or contains `videos`: List videos in the default index
- If `$ARGUMENTS` contains `indexes`: List all available indexes

### For "videos" (Default Behavior)

#### Step 2a: Call the List Videos MCP Tool

Use the `mcp__twelvelabs-mcp__list-videos` tool to get videos from the default index:

```
Tool: mcp__twelvelabs-mcp__list-videos
Parameters: (none - uses default index)
```

If you need to list videos from a specific index, you can provide the indexId:
```
Tool: mcp__twelvelabs-mcp__list-videos
Parameters:
  indexId: "<index-id>"
```

#### Step 3a: Display Video Results

Format the video list clearly for the user:

```
Indexed Videos

| Video ID | Filename |
|----------|----------|
| <id1>    | <name1>  |
| <id2>    | <name2>  |
| ...      | ...      |

Total: <count> videos
```

**If pagination is available** (indicated by `page` info in response):
```
Indexed Videos (Page 1)

| Video ID | Filename |
|----------|----------|
| <id1>    | <name1>  |
| ...      | ...      |

Showing <count> videos. More videos available - to see the next page, run:
/twelvelabs:list videos (and ask for the next page)
```

**If no videos found**:
```
No indexed videos found.

To index a video, use: /twelvelabs:index <path-or-url>
```

### For "indexes" Subcommand

#### Step 2b: Call the List Indexes MCP Tool

Use the `mcp__twelvelabs-mcp__list-indexes` tool:

```
Tool: mcp__twelvelabs-mcp__list-indexes
Parameters: (none)
```

#### Step 3b: Display Index Results

Format the index list clearly for the user:

```
Available Indexes

| Index ID | Name | Models |
|----------|------|--------|
| <id1>    | <name1> | <models1> |
| <id2>    | <name2> | <models2> |
| ...      | ...     | ...       |

Total: <count> indexes
```

**Model types**:
- `generative` - For analysis (summaries, chapters, highlights, etc.)
- `embedding` - For search and video embeddings

**If pagination is available**:
```
Available Indexes (Page 1)

| Index ID | Name | Models |
|----------|------|--------|
| ...      | ...  | ...    |

Showing <count> indexes. More indexes available - ask to see the next page.
```

**If no indexes found**:
```
No indexes found.

A default index will be created automatically when you index your first video.
To index a video, use: /twelvelabs:index <path-or-url>
```

## Important Notes

- **Default Index**: When no index ID is specified, the TwelveLabs MCP uses the default index
- **Video IDs**: These IDs are needed for search and analysis commands
- **Index Models**: Different indexes may have different capabilities based on their model configuration
- **Pagination**: Large lists may be paginated - ask to see more if needed

## Related Commands

- `/twelvelabs:index` - Index a new video
- `/twelvelabs:status` - Check indexing task status
- `/twelvelabs:search` - Search indexed videos
- `/twelvelabs:analyze` - Analyze indexed videos
