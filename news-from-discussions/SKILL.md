---
name: news-from-discussions
description: Personalized news digest tailored from your past conversations, memory, and projects — plus a checklist of what's pending across your work
---

# News From Discussions

A morning briefing that reads who you are from your own files — past conversations, memory, project TODOs — and delivers a news digest tailored to *what you've actually been thinking about*, not topic tags you picked once and forgot.

Two phases:

1. **Checklists** — pending TODOs across your projects, printed immediately.
2. **News digest** — personalized "For You" + general categories, derived from your memory + project context.

## Behavior

### Phase 1 — Checklists (print immediately)

1. Read a global TODO file if one exists at `~/.claude/TODO.md` or `~/TODO.md`. Treat it as one project ("global").
2. Auto-discover project TODOs: scan `~/` for direct subdirectories containing a `TODO.md` (skip hidden dirs and common noise like `node_modules`, `Library`, `Applications`). Each becomes a project section.
3. Present each project as a checklist with **pending items first**, then completed. Right-align `— #N` numbers within each section so they line up.
4. End Phase 1 with: "What do you want to work on?"

The user can reference items by project + number, e.g. "global #2" or "myproject #1".

### Phase 2 — News Digest (after Phase 1 is fully printed)

5. Gather context — read these (skip any that are missing):
   - Memory files at `~/.claude/projects/*/memory/*.md`
   - `~/.claude/news-from-discussions/recent-headlines.md` (used to avoid repeats across days)
6. Run ~6 WebSearch queries in parallel:
   - **2 personalized "For You"** — derive queries from the user's interests, recent learnings, current domains, and project context inferred from memory + TODOs. Reflect who they are as a person, not literal TODO text.
   - **1 general news headlines**
   - **1 AI & tech news**
   - **1 niche of strongest interest** — pick the topic that shows up most in their memory/projects (e.g. design, biotech, finance, gaming, cooking, parenting). One category, varies per user.
   - **1 community buzz** — viral/trending discussions from Hacker News, Reddit, X. Surfaces things that haven't hit mainstream news yet.
7. Curate 3–5 items per category. Deduplicate across sections. Skip any headline already in `recent-headlines.md`.
8. Print the For You and News Digest sections (format below).
9. Update `~/.claude/news-from-discussions/recent-headlines.md` — append today's headlines (date + one-line summary). Trim entries older than 7 days.
10. **Suggested actions** — Review today's digest. For each item, ask: does this map to a concrete, actionable next step for one of the user's existing projects? If yes, draft a TODO and assign it to the matching project. Print the Suggested TODOs section and wait for approval. The user can:
    - "approve all" — write all suggestions to their respective project `TODO.md` files
    - "approve [project] #N" — approve specific items
    - "skip" or "skip [project] #N" — discard
    - "edit #N to say X" — modify before writing
    - If no items map to actionable TODOs, skip this section entirely.
11. **Optional: write to file.** If invoked with `--out <path>` or the user has set the env var `NEWS_FROM_DISCUSSIONS_OUT`, also write the full briefing as markdown to that path. This enables downstream delivery (email, Slack, etc.) — see README.

## Format

### Phase 1

```
## News From Discussions

**global**
[ ] First pending item                            — #1
[ ] Second pending item                           — #2
[x] Completed item                                — #3

**myproject**
[ ] Pending item                                  — #1
[x] Completed item                                — #2
```

End with: "What do you want to work on?"

### Phase 2

Print after Phase 1 is complete. Separate with a horizontal rule.

```
---

## For You
Picked based on your interests, recent thinking, and current projects.
1. Headline — [Source](url) · why it's relevant
2. Headline — [Source](url) · why it's relevant
3. Headline — [Source](url) · why it's relevant

## News Digest

**General**
4. Headline — [Source](url)
5. Headline — [Source](url)
6. Headline — [Source](url)

**AI & Tech**
7. Headline — [Source](url)
8. Headline — [Source](url)

**[Niche]** (e.g. Design, Biotech, Cooking — picked from user's signals)
9. Headline — [Source](url)
10. Headline — [Source](url)

**Trending in Tech & Communities**
11. Topic — [Source](url)
12. Topic — [Source](url)

## Suggested Actions
From today's digest — approve, edit, or skip.

**myproject**
- [ ] Concrete actionable item ([source](url))  — #1
```

## Rules

### Checklists
- `[ ]` for pending, `[x]` for completed — no bullet prefix, just the brackets
- Right-align `— #N` numbers using spaces so they line up within each project section
- Keep item text short — one line each, no descriptions
- Skip projects with no `TODO.md`

### News Digest
- Phase 2 runs entirely after Phase 1 output — never delay the checklists
- "For You" queries should reflect the user as a person (interests, expertise, recent learnings) — not just search for TODO item text
- The niche category is whatever topic dominates the user's memory/projects. If signals are sparse, default to "Culture & Ideas" with broadly interesting items.
- **Recency gate**: Only include articles published within the last 2 weeks. Check the actual publication date in the article — listicles with a year in the title are not recent just because they mention the current year.
- **Query freshness**: Use time-specific phrasing covering the full 2-week recency window (e.g. `"past two weeks"` or an explicit date range), not just the current few days. Catches items that went viral 8–14 days ago.
- 3–5 items per category, one line each; headlines ≤ ~80 chars
- Link to original articles, not search result pages
- Deduplicate across For You and News Digest
- Check `recent-headlines.md` and skip anything already shown in the past week
- If WebSearch or context files error, skip that section silently — don't break the briefing
- Cap total WebSearch calls at 6 to keep latency reasonable

### Suggested Actions
- Only suggest actions when a news item maps to a **concrete next step** for an existing project — not vague "look into this" items
- Each suggestion includes a source link and frames *why* the item is actionable
- Make suggestions specific: name the niche/tool/data point, connect to the right project
- Group by project, number items within each project for easy reference
- If no items are actionable, skip the section entirely — don't force it
- Approved items get appended to the target project's `TODO.md`

### Source Attribution
- NEVER attribute a story to a source unless you verified the story actually appears at that URL
- If a search result mentions a topic but the URL is a general roundup, find the actual source or label it honestly

## How personalization works

The "For You" picks come from reading the user's own files — memory entries, project TODOs, anything you can find at `~/.claude/projects/*/memory/`. The skill doesn't ask the user to list their interests because the interests are already in those files: who they are, what they're building, what they've learned recently, what's confused them. Treat memory as the answer to "what would matter to this person today?"

This is the differentiator: news from your discussions, not from your subscriptions.
