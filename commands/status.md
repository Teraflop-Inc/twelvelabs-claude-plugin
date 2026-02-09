---
name: status
description: Check the status of video indexing tasks on TwelveLabs
disable-model-invocation: true
argument-hint: [task-id]
---

# /twelvelabs:status - Check Video Indexing Status

Check the status of video indexing tasks.

## Usage

```
/twelvelabs:status [task-id]
```

**User provided:** `$ARGUMENTS`

## Arguments

- `task-id` - **Optional**. Specific task ID to check. If not provided, shows all pending tasks.

## Examples

### Check All Pending Tasks
```
/twelvelabs:status
```

### Check Specific Task
```
/twelvelabs:status 6789abcd-1234-5678-9012-abcdef123456
```

## Status Values

Tasks progress through these statuses:

| Status | Description |
|--------|-------------|
| `validating` | Video uploaded, being validated (format, duration, etc.) |
| `pending` | Validation complete, waiting for worker server |
| `queued` | Worker assigned, preparing to index |
| `indexing` | Transforming video into embeddings |
| `ready` | Indexing complete, video ready for search/analysis |
| `failed` | Indexing failed (check error message) |

## Instructions for Claude

When the user invokes `/twelvelabs:status`, the optional argument is provided in `$ARGUMENTS`. Follow these steps:

### Step 1: Parse Arguments

Check if a task ID was provided in `$ARGUMENTS`:
- If `$ARGUMENTS` contains a task ID, use it to check a specific task
- If `$ARGUMENTS` is empty, check all pending tasks

### Step 2: Read Local Pending Tasks

First, check the local config for any tracked pending tasks:

```bash
python3 -c "
import sys
sys.path.insert(0, '.twelvelabs')
from config_helper import get_all_pending_tasks
import json
print(json.dumps(get_all_pending_tasks(), indent=2))
"
```

This returns a dict of pending tasks keyed by task_id, or an empty dict if none.

### Step 3: Determine What to Check

#### If `$ARGUMENTS` contains a task-id:
- Check only that specific task using the MCP tool

#### If `$ARGUMENTS` is empty (no task-id):
- If there are pending tasks in local config, check those
- If no pending tasks in local config, check the latest 10 tasks from the API

### Step 4: Call the MCP Tool

Use the `mcp__twelvelabs-mcp__get-video-indexing-tasks` tool:

#### For a specific task:
```
Tool: mcp__twelvelabs-mcp__get-video-indexing-tasks
Parameters:
  taskId: "<task-id>"
```

#### For all recent tasks (no task-id):
```
Tool: mcp__twelvelabs-mcp__get-video-indexing-tasks
Parameters: (none - returns latest 10 tasks)
```

### Step 5: Update Local Config (if tasks completed)

For each task returned:

1. **If status is "ready"**:
   - Extract the `video_id` from the response
   - Move task from pending to videos in config:
   ```bash
   python3 -c "
   import sys
   sys.path.insert(0, '.twelvelabs')
   from config_helper import complete_task
   complete_task('<task_id>', '<video_id>', '<filename>')
   "
   ```

2. **If status is "failed"**:
   - Remove task from pending in config:
   ```bash
   python3 -c "
   import sys
   sys.path.insert(0, '.twelvelabs')
   from config_helper import fail_task
   fail_task('<task_id>')
   "
   ```

3. **If status is still in progress** (validating, pending, queued, indexing):
   - Update the status in local config:
   ```bash
   python3 -c "
   import sys
   sys.path.insert(0, '.twelvelabs')
   from config_helper import update_pending_task_status
   update_pending_task_status('<task_id>', '<status>')
   "
   ```

### Step 6: Display Results

Format the status information clearly for the user:

#### For a specific task:
```
Task Status: <task-id>

Status: <status>
Source: <source-path-or-url>
Started: <timestamp>

[If ready]
Video is ready for search and analysis!
Video ID: <video_id>

[If failed]
Indexing failed. Check the video file and try again.

[If in progress]
Video is still being processed. Check again later.
```

#### For multiple tasks:
```
Indexing Task Status

| Task ID | Source | Status | Started |
|---------|--------|--------|---------|
| <id1>   | <src1> | <sts1> | <time1> |
| <id2>   | <src2> | <sts2> | <time2> |

Legend:
- ready - Video indexed and ready
- indexing/queued/pending/validating - In progress
- failed - Check error and retry
```

#### If no tasks found:
```
No indexing tasks found.

To index a video, use: /twelvelabs:index <path-or-url>
```

## Important Notes

- **Async Processing**: Video indexing runs asynchronously on TwelveLabs servers
- **Processing Time**: Indexing can take several minutes depending on video length
- **Status Sync**: Local config is updated when you check status
- **Ready Videos**: Once ready, videos can be searched with `/twelvelabs:search` or analyzed with `/twelvelabs:analyze`

## Related Commands

- `/twelvelabs:index` - Start indexing a new video
- `/twelvelabs:list` - List all indexed videos
- `/twelvelabs:search` - Search indexed videos
- `/twelvelabs:analyze` - Analyze indexed videos
