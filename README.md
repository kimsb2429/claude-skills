# Deep Dive

A Claude Code skill for autonomous deep research. Decomposes any question into a dependency graph (DAG) of sub-questions, researches them in parallel via subagents, identifies gaps, iterates, and synthesizes a final report.

Inspired by Google's Deep Research — but runs entirely inside Claude Code with no external APIs or dependencies. Just a markdown file.

## How it works

```
Research question
    |
    v
DAG Planning — decompose into sub-questions with dependencies
    |
    v
Wave 1 — launch independent questions as parallel subagents
    |
    v
Wave 2+ — launch dependent questions with prior findings as context
    |
    v
Gap Analysis — review what's missing, run 1 follow-up wave if needed
    |
    v
Synthesis — combine all findings into a coherent report
    |
    v
Save — persist report as markdown in project docs
```

### What makes it different

Most research skills use a **linear pipeline** — search, then summarize. Deep Dive uses a **dependency-ordered DAG**, meaning:

- Questions that depend on other answers wait for those answers first
- Independent questions run simultaneously (3-6 subagents in parallel)
- Context flows forward — later questions receive findings from earlier ones
- The research plan adapts based on what's found, not a fixed sequence

This is the architecture Google uses for Deep Research. Among [7+ community alternatives surveyed](https://github.com/mediar-ai/skillhubz), this is the only Claude Code skill that implements true DAG-based planning.

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

1. **DAG plan** — printed as a table before execution starts, so you can see the research strategy
2. **Real-time progress** — task checklist at the bottom of the terminal tracks each subagent
3. **Gap iteration** — after the first pass, identifies what's missing and runs targeted follow-ups
4. **Final report** — synthesized markdown saved to `docs/deep-dive/` in your project directory with:
   - Executive summary
   - Themed findings (not just concatenated subagent outputs)
   - Open questions (honest about what wasn't resolved)
   - Deduplicated source list

## Example output

A typical deep-dive produces a 500-2000 line report covering 4-8 sub-questions, with 15-40 cited sources, in 5-15 minutes. Reports are saved as timestamped markdown files:

```
docs/deep-dive/
  2026-04-02-jira-docs-from-microservices.md
  2026-04-02-downloadable-multi-agent-frameworks.md
  2026-04-06-business-rule-extraction-skills.md
```

## Requirements

- Claude Code CLI (any version with Agent subagent support)
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

The skill is a single markdown file — edit it to fit your workflow:

- Change the report output directory (default: `docs/deep-dive/`)
- Adjust the max node count (default: 4-8, cap at 12)
- Modify the subagent prompt template
- Add domain-specific instructions for specialized research

## License

MIT
