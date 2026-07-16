# Weekly Design Update

Generate a weekly design status report as an interactive HTML file, pull GitHub issues from the project board, and post comments to issues after review.

## When to use

- "generate my weekly report"
- "weekly design update"
- "what did I work on this week"
- "post comments to my issues"
- "update my design board"

## Prerequisites

- `gh` CLI authenticated with an EMU account that has access to `coreai-microsoft` org
- WorkIQ plugin installed (for pulling M365 evidence)
- Project board: `coreai-microsoft` org, project #35

## Workflow

### Step 1: Gather evidence

Use WorkIQ to query the user's past week:
- Teams messages about design decisions, reviews, and feedback
- Emails with stakeholders about feature direction
- Documents edited (Figma links, PRDs, specs)
- Meetings attended (design reviews, syncs)

### Step 2: Pull GitHub issues

```bash
gh auth switch --user <EMU_USERNAME>
gh search issues --assignee <EMU_USERNAME> --owner coreai-microsoft --state open --json title,url --limit 50
```

### Step 3: Generate the report

Use the HTML template at `template.html` in this skill folder. Populate the `weeks` array with:

Each item needs:
- `name`: Feature name (matches GitHub issue title)
- `issue`: GitHub issue URL
- `status`: Current phase — "Fit and finish" | "Iteration" | "Early exploration" | "Kickoff" | "On pause"
- `statusClass`: CSS class — "done" | "progress" | "kickoff" | "pause" | "warn"
- `links`: Array of `{label, href}` for related docs
- `update`: 1-2 sentence summary of what happened this week
- `details`: Array of bullet strings (key decisions, scope changes)
- `next`: What's coming next week
- `gallery`: boolean — whether screenshots are expected

Group items into sections by phase:
- **Fit and finish** — nearly shipped, polishing
- **Work in progress** — actively iterating
- **On pause** — blocked on someone else

### Step 4: Post to GitHub issues (on user request)

For each item, generate a markdown comment from its data:

```markdown
**Weekly Design Update — [date range]**

**Status:** [status]

**Update:** [update field]

[details as bullet list if present]

**Next step:** [next field]
```

Post via:
```bash
gh api repos/coreai-microsoft/caidr/issues/ISSUE_NUMBER/comments -X POST -f body='...'
```

## Report features

The HTML template supports:
- Click card → Cmd+V to paste screenshots (persisted in localStorage)
- Drag screenshots between cards
- Click screenshot for lightbox zoom
- Pencil icon to edit links inline
- Multi-week dropdown selector
- Status pills with phase labels
- Update / Details / Next step structured layout

## Configuration

Each user should have a `config.json` (or just tell the agent):
- Their GitHub EMU username
- The org and project board number
- Their preferred section grouping

## Sharing

Place this skill folder in any shared repo's `.copilot/skills/` directory. Colleagues who clone the repo will automatically have the skill available in VS Code Copilot Chat.
