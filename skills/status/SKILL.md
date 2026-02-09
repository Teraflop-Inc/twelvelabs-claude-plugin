---
name: status
description: Check the status of video indexing tasks. Use when user asks about indexing progress, says "is my video ready?", "how long until indexing is done?", or wants to know if they can search/analyze yet.
---

# Check Video Indexing Status

Check the status of video indexing tasks to see if videos are ready for search and analysis.

## When to Use This Skill

Use this skill when the user:
- Asks if their video is ready ("is my video ready?", "can I search my video yet?")
- Wants to know indexing progress ("how long until it's done?", "what's the status?")
- Asks about a pending indexing task ("is it still processing?")
- Wants to check on a specific task ("check task xyz")

## Instructions

### Step 1: Determine What to Check

Based on the user's request:

- **Specific task mentioned**: Check that specific task ID
- **General status question**: Check all recent/pending tasks

### Step 2: Call the MCP Tool

Use the `mcp__twelvelabs-mcp__get-video-indexing-tasks` tool:

**For a specific task**:
```
Tool: mcp__twelvelabs-mcp__get-video-indexing-tasks
Parameters:
  taskId: "<task-id>"
```

**For all recent tasks** (no specific task mentioned):
```
Tool: mcp__twelvelabs-mcp__get-video-indexing-tasks
Parameters: (none - returns latest 10 tasks)
```

### Step 3: Interpret Status Values

Tasks progress through these statuses:

| Status | Meaning | User-Friendly Message |
|--------|---------|----------------------|
| `validating` | Video uploaded, being validated | "Your video is being validated..." |
| `pending` | Waiting for processing server | "Your video is queued for processing..." |
| `queued` | Server assigned, preparing | "Processing is about to start..." |
| `indexing` | Converting to searchable format | "Your video is being indexed..." |
| `ready` | Complete! | "Your video is ready!" |
| `failed` | Something went wrong | "Indexing failed. Please try again." |

### Step 4: Display Results

**For a single task (ready)**:
```
Your video is ready!

Task ID: <task-id>
Video ID: <video-id>
Status: Ready

You can now:
- Search the video: "find the part where..."
- Analyze it: "summarize this video" or "create chapters"
```

**For a single task (in progress)**:
```
Your video is still processing.

Task ID: <task-id>
Status: <status>

<User-friendly status message from table above>

Check back in a few minutes.
```

**For a single task (failed)**:
```
Indexing failed.

Task ID: <task-id>
Status: Failed
Error: <error message if available>

This might happen if:
- The video is too short (minimum 4 seconds)
- The video format is not supported
- The URL is not accessible

Please try indexing again with a different video or check the source.
```

**For multiple tasks**:
```
Indexing Task Status

| Status | Source | Task ID |
|--------|--------|---------|
| Ready | demo.mp4 | abc123 |
| Indexing | meeting.mp4 | def456 |
| Queued | tutorial.mp4 | ghi789 |

Ready videos can be searched and analyzed.
In-progress videos will be available soon.
```

**No tasks found**:
```
No indexing tasks found.

To index a video, just say:
"Index this video: <path-or-url>"
```

## Example Interactions

**User**: "Is my video ready?"
**Action**: Call get-video-indexing-tasks (no params) → show status of recent tasks

**User**: "Is my video ready yet? I uploaded it 5 minutes ago"
**Action**: Call get-video-indexing-tasks → show status with context that indexing takes time

**User**: "Check status of task abc123"
**Action**: Call get-video-indexing-tasks with taskId: "abc123"

**User**: "Can I search my demo.mp4 now?"
**Action**: Call get-video-indexing-tasks → find the task for demo.mp4 → report status

## Important Notes

- **Processing Time**: Indexing typically takes several minutes depending on video length
- **Async Nature**: Indexing happens in the background - users can check back anytime
- **Ready for Use**: Only videos with "ready" status can be searched and analyzed
- **Failed Tasks**: Suggest re-indexing with a different video or checking the source
