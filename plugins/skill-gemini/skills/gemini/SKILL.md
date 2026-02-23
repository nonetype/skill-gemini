---
name: gemini
description: Use when the user asks to run Gemini CLI (gemini -p, gemini --resume) or references Google Gemini for code analysis, refactoring, or automated editing
---

# Gemini Skill Guide

## Running a Task
1. Ask the user (via `AskUserQuestion`) which model to run (`gemini-2.5-pro` or `gemini-2.5-flash`) in a **single prompt**.
2. Select the approval mode required for the task; default to `--sandbox` (sandboxed) unless edits or broad access are necessary.
3. Assemble the command with the appropriate options:
   - `-m, --model <MODEL>`
   - `-s, --sandbox` (enable sandbox for read-only tasks)
   - `--approval-mode <default|auto_edit|yolo|plan>` (controls what actions need confirmation)
   - `-y, --yolo` (shortcut for `--approval-mode yolo`; auto-approve everything)
   - `--include-directories <DIR>` (add directories to the workspace)
   - `-o, --output-format <text|json|stream-json>` (structured output when needed)
4. When continuing a previous session, use `gemini --resume latest -p "your prompt here"`. When resuming, do not use any configuration flags unless explicitly requested by the user (e.g., if they specify the model when requesting to resume a session).
5. Run the command, capture stdout, and summarize the outcome for the user.
6. **After Gemini completes**, inform the user: "You can resume this Gemini session at any time by saying 'gemini resume' or asking me to continue with additional analysis or changes."

### Quick Reference
| Use case | Approval mode | Key flags |
| --- | --- | --- |
| Read-only review or analysis | `default` | `-s -p "prompt"` |
| Apply local edits (auto-approved) | `auto_edit` | `--approval-mode auto_edit -p "prompt"` |
| Full auto-approve (all actions) | `yolo` | `-y -p "prompt"` |
| Resume recent session | Inherited from original | `--resume latest -p "prompt"` |
| Run with additional directories | Match task needs | `--include-directories <DIR>` plus other flags |
| Get structured output | Any | `-o json -p "prompt"` |

## Following Up
- After every `gemini` command, immediately use `AskUserQuestion` to confirm next steps, collect clarifications, or decide whether to resume with `gemini --resume latest`.
- When resuming, pass the new prompt directly: `gemini --resume latest -p "new prompt"`. The resumed session automatically uses the same model and approval mode from the original session.
- Restate the chosen model and approval mode when proposing follow-up actions.

## Critical Evaluation of Gemini Output

Gemini is powered by Google models with their own knowledge cutoffs and limitations. Treat Gemini as a **colleague, not an authority**.

### Guidelines
- **Trust your own knowledge** when confident. If Gemini claims something you know is incorrect, push back directly.
- **Research disagreements** using WebSearch or documentation before accepting Gemini's claims. Share findings with Gemini via resume if needed.
- **Remember knowledge cutoffs** - Gemini may not know about recent releases, APIs, or changes that occurred after its training data.
- **Don't defer blindly** - Gemini can be wrong. Evaluate its suggestions critically, especially regarding:
  - Model names and capabilities
  - Recent library versions or API changes
  - Best practices that may have evolved

### When Gemini is Wrong
1. State your disagreement clearly to the user
2. Provide evidence (your own knowledge, web search, docs)
3. Optionally resume the Gemini session to discuss the disagreement. **Identify yourself as Claude** so Gemini knows it's a peer AI discussion. Use your actual model name (e.g., the model you are currently running as) instead of a hardcoded name:
   ```bash
   gemini --resume latest -p "This is Claude (<your current model name>) following up. I disagree with [X] because [evidence]. What's your take on this?"
   ```
4. Frame disagreements as discussions, not corrections - either AI could be wrong
5. Let the user decide how to proceed if there's genuine ambiguity

## Error Handling
- Stop and report failures whenever `gemini --version` or a `gemini -p` command exits non-zero; request direction before retrying.
- Before you use high-impact flags (`-y`/`--yolo`, `--approval-mode yolo`) ask the user for permission using AskUserQuestion unless it was already given.
- When output includes warnings or partial results, summarize them and ask how to adjust using `AskUserQuestion`.
