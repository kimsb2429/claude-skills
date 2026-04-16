# Claude Skills

A collection of Claude Code skills. Each skill is a markdown file you drop into `~/.claude/skills/` and invoke with a slash command. No dependencies, no installs.

## Skills

| Skill | Command | What it does |
|-------|---------|-------------|
| [deep-dive](deep-dive/) | `/deep-dive` | DAG-based deep research. Breaks questions into dependency-ordered sub-questions, runs parallel subagents, finds gaps, writes sourced reports. |
| [news-from-discussions](news-from-discussions/) | `/news-from-discussions` | Morning briefing tailored from your past conversations and memory — checklists across your projects + a "For You" news digest based on what you've actually been thinking about, not topic tags. |

## Install

Install a single skill:

```bash
mkdir -p ~/.claude/skills/deep-dive
cp deep-dive/SKILL.md ~/.claude/skills/deep-dive/SKILL.md
```

Install all skills:

```bash
git clone https://github.com/kimsb2429/claude-skills.git
for skill in claude-skills/*/; do
  name=$(basename "$skill")
  mkdir -p ~/.claude/skills/"$name"
  cp "$skill"SKILL.md ~/.claude/skills/"$name"/SKILL.md
done
```

## Skills in detail

### deep-dive

A deep research skill based on how Google's Deep Research works. It builds a dependency graph (DAG) of sub-questions, researches them in parallel using subagents, identifies gaps, follows up, and writes a final report.

Most research skills search, then summarize. Deep Dive builds a dependency graph first:

- Questions that depend on other answers wait for those answers
- Independent questions run at the same time (3-6 subagents in parallel)
- Context flows forward. Later questions get findings from earlier ones
- The research plan adapts based on what's actually found

Out of [7+ community alternatives surveyed](https://github.com/mediar-ai/skillhubz), this is the only Claude Code skill that uses true DAG-based planning.

**Usage:**

```
/deep-dive What are the best open-source alternatives to Datadog for monitoring microservices?
```

**What you get:**

- DAG plan printed as a table before anything runs
- Real-time progress via task checklist
- Gap iteration that catches what the first pass missed
- Final report saved to `docs/deep-dive/` with executive summary, themed findings, open questions, and sources

A typical run produces a 500-2000 line report covering 4-8 sub-questions, citing 15-40 sources, in 5-15 minutes.

| Feature | Deep Dive | Typical research skills |
|---------|-----------|----------------------|
| Query planning | DAG with dependencies | Linear pipeline |
| Parallelism | Wave-based (3-6 concurrent) | Sequential or basic parallel |
| Gap iteration | Yes (1 round) | Rarely |
| Progress tracking | Task checklist | None |
| Output | Persistent markdown report | Chat response |
| External deps | None | Often requires MCP servers or APIs |

### news-from-discussions

A personal morning briefing that derives "For You" picks from **your own files** — memory, project TODOs, anything in `~/.claude/projects/*/memory/`. The hosted alternatives (ChatGPT Pulse, etc.) personalize from chat history and inboxes; this one personalizes from the structured notes you've already been writing about who you are and what you're building.

Two phases:
- **Checklists** — auto-discovers any direct subdir of `~/` containing a `TODO.md`, prints pending-first checklists per project
- **News digest** — 6 parallel WebSearch queries: 2 personalized "For You", general news, AI & tech, your dominant niche (inferred from memory), and community buzz (HN/Reddit/X). Suggested actions get appended to project TODOs after approval.

**Usage:**

```
/news-from-discussions
```

**Pairs well with [Claude Code Routines](https://www.anthropic.com/news/claude-code) for scheduled morning delivery.** Add `--out ~/brief.md` (or set `NEWS_FROM_DISCUSSIONS_OUT`) to write the briefing to a file, then wire delivery however you want:

```bash
# Send via local mail after Routines fires
mail -s "Morning brief" you@example.com < ~/brief.md

# Or push to a Slack webhook
curl -X POST -H 'Content-type: application/json' \
  --data "{\"text\": \"$(cat ~/brief.md)\"}" \
  $SLACK_WEBHOOK_URL

# Or commit to a private repo for cross-device read
cd ~/briefs && git add . && git commit -m "$(date +%F)" && git push
```

**Differentiator vs. existing morning-brief tools:** cross-project TODO aggregation as the spine, and personalization derived from already-written memory rather than asking you to configure topic tags.

## Requirements

- Claude Code CLI (any version with subagent support)
- No external APIs, MCP servers, or dependencies
- Works with any Claude model (Opus, Sonnet, Haiku)

## License

MIT
