Leave a star ⭐ if you like it 😘

# Gemini Integration for Claude Code

<img width="2288" height="808" alt="skillgemini" src="https://github.com/user-attachments/assets/85336a9f-4680-479e-b3fe-d6a68cadc051" />


## Purpose
Enable Claude Code to invoke the Gemini CLI (`gemini -p` and session resumes) for automated code analysis, refactoring, and editing workflows.

## Prerequisites
- `gemini` CLI installed and available on `PATH`.
- Gemini configured with valid credentials and settings.
- Confirm the installation by running `gemini --version`; resolve any errors before using the skill.

## Installation

This repository is structured as a [Claude Code Plugin](https://code.claude.com/docs/en/plugins) with a marketplace. You can install it as a **plugin** (recommended) or extract it as a **standalone skill**.

### Option 1: Plugin Installation (Recommended)

Install via Claude Code's plugin system for automatic updates:

```
/plugin marketplace add nonetype/skill-gemini
/plugin install skill-gemini@skill-gemini
```

### Option 2: Standalone Skill Installation

Extract the skill folder manually:

```
git clone --depth 1 git@github.com:nonetype/skill-gemini.git /tmp/skills-temp && \
mkdir -p ~/.claude/skills && \
cp -r /tmp/skills-temp/plugins/skill-gemini/skills/gemini ~/.claude/skills/gemini && \
rm -rf /tmp/skills-temp
```

## Usage

### Output Formats
By default, Gemini outputs plain text. If you need structured output for programmatic processing, you can ask Claude to use `--output-format json` or `--output-format stream-json`. Use the `--debug` flag if you need verbose diagnostic output.

### Example Workflow

**User prompt:**
```
Use gemini to analyze this repository and suggest improvements for my claude code skill.
```

**Claude Code response:**
Claude will activate the Gemini skill and:
1. Ask which model to use (`gemini-2.5-pro` or `gemini-2.5-flash`) unless already specified in your prompt.
2. Select appropriate approval mode (defaults to `--sandbox` for analysis)
3. Run a command like:
```bash
gemini -m gemini-2.5-pro \
  -s \
  -p "Analyze this Claude Code skill repository comprehensively..."
```

**Result:**
Claude will summarize the Gemini analysis output, highlighting key suggestions and asking if you'd like to continue with follow-up actions.

### Detailed Instructions
See [`plugins/skill-gemini/skills/gemini/SKILL.md`](plugins/skill-gemini/skills/gemini/SKILL.md) for complete operational instructions, CLI options, and workflow guidance.

## License

MIT License - see [LICENSE](LICENSE) for details.
