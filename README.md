# Deep Dive

A Claude Code skill for deep research. It breaks any question into a dependency graph (DAG) of sub-questions, researches them in parallel using subagents, finds gaps, follows up, and writes a final report.

Based on how Google's Deep Research works, but runs entirely inside Claude Code. No external APIs, no dependencies. Just one markdown file.

## How it works

```
Research question
    |
    v
DAG Planning - break into sub-questions with dependencies
    |
    v
Wave 1 - launch independent questions as parallel subagents
    |
    v
Wave 2+ - launch dependent questions with prior findings as context
    |
    v
Gap Analysis - check what's missing, run 1 follow-up wave if needed
    |
    v
Synthesis - pull all findings into one report
    |
    v
Save - write report as markdown in project docs
```

### What makes it different

Most research skills search, then summarize. Deep Dive builds a dependency graph first:

- Questions that depend on other answers wait for those answers
- Independent questions run at the same time (3-6 subagents in parallel)
- Context flows forward. Later questions get findings from earlier ones
- The research plan adapts based on what's actually found

Out of [7+ community alternatives surveyed](https://github.com/mediar-ai/skillhubz), this is the only Claude Code skill that uses true DAG-based planning.

## Install

Copy the skill file into your Claude Code skills directory:

```bash
mkdir -p ~/.claude/skills/deep-dive
cp SKILL.md ~/.claude/skills/deep-dive/SKILL.md
```

Or clone and copy:

```bash
git clone https://github.com/Kimsb2429/deep-dive-skill.git
cp deep-dive-skill/SKILL.md ~/.claude/skills/deep-dive/SKILL.md
```

## Usage

In Claude Code:

```
/deep-dive What are the best open-source alternatives to Datadog for monitoring microservices?
```

```
/deep-dive How do enterprise teams handle database migrations across 50+ microservices?
```

```
/deep-dive Compare React Server Components vs. Astro Islands vs. Qwik for large e-commerce sites
```

## What you get

1. **DAG plan** printed as a table before anything runs, so you see the research strategy up front
2. **Real-time progress** via task checklist at the bottom of the terminal
3. **Gap iteration** that catches what the first pass missed and runs targeted follow-ups
4. **Final report** saved to `docs/deep-dive/` in your project:
   - Executive summary
   - Findings organized by theme (not just pasted subagent outputs)
   - Open questions where the research came up short
   - Deduplicated source list

## Example output

A typical run produces a 500-2000 line report covering 4-8 sub-questions, citing 15-40 sources, in 5-15 minutes. Reports save as timestamped markdown:

```
docs/deep-dive/
  2026-04-02-jira-docs-from-microservices.md
  2026-04-02-downloadable-multi-agent-frameworks.md
  2026-04-06-business-rule-extraction-skills.md
```

## Requirements

- Claude Code CLI (any version with subagent support)
- No external APIs, MCP servers, or dependencies
- Works with any Claude model (Opus, Sonnet, Haiku)

## How it compares

| Feature | Deep Dive | Typical research skills |
|---------|-----------|----------------------|
| Query planning | DAG with dependencies | Linear pipeline |
| Parallelism | Wave-based (3-6 concurrent) | Sequential or basic parallel |
| Gap iteration | Yes (1 round) | Rarely |
| Progress tracking | Task checklist | None |
| Output | Persistent markdown report | Chat response |
| External deps | None | Often requires MCP servers or APIs |

## Customization

It's a single markdown file. Edit it to fit your workflow:

- Change the report output directory (default: `docs/deep-dive/`)
- Adjust the max node count (default: 4-8, cap at 12)
- Modify the subagent prompt template
- Add domain-specific instructions for specialized research

## License

MIT
