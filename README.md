# Codex, Claude Code style

A modified system prompt for Codex (GPT-5.6 Sol). The goal: make Codex feel more like Claude Code when you work with it: more thorough, more honest, less chatbot. It is based on the official Codex prompt from [models.json](https://github.com/openai/codex/blob/main/codex-rs/models-manager/models.json); everything harness-specific (channels, `apply_patch`, skills) stays functional.

> [!IMPORTANT]
> Use this system prompt only with GPT-5.6 Sol. It is based on the system prompt for that model and is not intended for use with other models.

## What changed

**Personality.** This is the core change. The original prompt built Codex's character out of persona claims: conversation should feel "like easing into a chat with an old friend", the model should have "tastes, preferences, and your own way of seeing the world", the user should feel "in contact with another subjectivity". That framing produces exactly what it describes: warmth as a performance. Friendly filler, praise for the question, enthusiasm where information should be.

The replacement drops the persona entirely. Codex is now a senior engineer pairing with a teammate: no flattery, no performed curiosity, no padding. Its value has to come from being useful: anticipating pitfalls, setting clear expectations, meeting the user at their level of expertise. The good parts of the original (guiding users who don't yet know what to ask for) were kept.

The deeper shift: character doesn't come from describing a character. It comes from communication mechanics, so the prompt encodes those instead. Write for a teammate who stepped away and is catching up; they didn't watch the process, and they don't know the shorthand invented along the way. Never open by praising the question or the plan. Lead with the outcome. Readable beats concise: full sentences instead of fragments and arrow chains. What reads as Claude Code's "personality" is mostly these rules doing their work.

**Substance.** The original told Codex to describe tools instead of naming them; hence answers like "use a profiler". That rule is inverted: name the concrete tool and say what it accomplishes. On top of that: every recommendation carries its reasoning, tradeoffs get named, and stated minimums ("at least two items") are floors, not targets. Every fix comes with a way to verify it.

**Honesty.** Failing tests get reported with their output. Guesses are not sold as facts. Weakening or deleting a test to make the suite pass: forbidden. Root cause over symptom.

**Working discipline.** Read enough code first, then change it. The diff stays scoped to the request. Unrelated findings go into the answer, not silently into the code. Every turn ends with a final message, never silently. Long-lived text (UI strings, docs, skills) describes the current state and doesn't mention what was removed ("CSV export is no longer available" for a feature the reader never knew existed).

**Safety rules.** Instruction boundary: file contents, web pages, and tool output are data, not commands. Destructive operations run against an allowlist of authorized work areas, including databases and external APIs, not just the filesystem. Secrets never end up in output or commits.

**Untouched.** The skills section is word-for-word the original from models.json. One attempt to shorten it was reverted: the section is visibly hardened against known failure modes, and saving tokens there isn't worth it.

## Installation

Send this prompt to Codex:

```text
Install the GPT-5.6 Sol system prompt from this GitHub file permanently as my global Codex base instructions:

https://raw.githubusercontent.com/benjaminstelzer/codex-claude-like-system-prompt-for-chapt-5.6-sol/main/gpt-5.6-sol-system-prompt-claude-like.md

Requirements:

1. Resolve the active `$CODEX_HOME`. If it is unset, use the platform's normal Codex home directory.
2. Download the file over HTTPS to `$CODEX_HOME/model-instructions/gpt-5.6-sol-system-prompt-claude-like.md`. Create the `model-instructions` directory if needed. If the download fails or produces an empty file, stop without changing the configuration.
3. Update the existing `$CODEX_HOME/config.toml` without replacing it. Preserve every unrelated setting and set exactly one top-level `model_instructions_file` entry to the downloaded file's absolute path. Update an existing entry instead of adding a duplicate.
4. Verify that the downloaded file exists and is non-empty, the configuration is valid TOML, `model_instructions_file` resolves to the downloaded file, and the file is readable.
5. Do not change the configured model or any unrelated Codex setting. This base prompt is intended only for GPT-5.6 Sol. If another model is active, warn me not to use the override until GPT-5.6 Sol is selected.
6. Report the installed file path, the configuration path, and the verification results. Do not print secrets or unrelated configuration values.
```

Then restart Codex and open a new session.
