---
name: google-image-gen
description: >
  Generate images using Google's Gemini API. Use this skill when the user wants
  to generate, create, or edit images with AI. Keywords: image generation,
  create image, generate picture, AI art, edit image, Gemini, icon, render,
  illustration.
allowed-tools: Bash, Read, Glob
---

# Google Image Generation Skill

Generate images from text prompts using Google's Gemini API.

## First-Time Setup (Once Per Context)

Run these commands once at the start of a session:

```bash
${CLAUDE_PLUGIN_ROOT}/scripts/check_env.sh
cd ${CLAUDE_PLUGIN_ROOT} && uv sync && cd -
```

If the environment check fails, the user needs to configure their API key.
Recommended: Create `~/.config/google-image-gen/.env` with:

```
GOOGLE_AI_API_KEY=your_key_here
```

Get an API key from https://aistudio.google.com/apikey

Alternative: Export as environment variable: `export GOOGLE_AI_API_KEY=your_key`

## Usage

**Important:** The plugin runs from its own directory. Use `--cwd` to ensure output files land in the user's project,
not the plugin cache.

```bash
ORIG_CWD="$(pwd)" && cd ${CLAUDE_PLUGIN_ROOT} && uv run python main.py --cwd "$ORIG_CWD" <output_path> "<prompt>" [options]
```

**Note:** When the user specifies a path like `output.png` or `images/photo.png`, pass it as-is — the `--cwd` parameter
ensures it resolves relative to the user's project root.

## Options

| Option     | Short | Description                                                             |
|------------|-------|-------------------------------------------------------------------------|
| `--style`  | `-s`  | Style template (.md file with `{subject}` placeholder)                  |
| `--ref`    | `-r`  | Reference image for style (repeatable, max 14)                          |
| `--edit`   | `-e`  | Edit existing image instead of generating                               |
| `--aspect` | `-a`  | Aspect ratio: `1:1`, `3:4`, `4:3`, `4:5`, `5:4`, `9:16`, `16:9`, `21:9` |

## Examples

### Simple Generation

```bash
ORIG_CWD="$(pwd)" && cd ${CLAUDE_PLUGIN_ROOT} && uv run python main.py --cwd "$ORIG_CWD" output.png "A red apple on a wooden table"
```

### Save to subdirectory

```bash
ORIG_CWD="$(pwd)" && cd ${CLAUDE_PLUGIN_ROOT} && uv run python main.py --cwd "$ORIG_CWD" images/output.png "A red apple"
```

### With Aspect Ratio

```bash
ORIG_CWD="$(pwd)" && cd ${CLAUDE_PLUGIN_ROOT} && uv run python main.py --cwd "$ORIG_CWD" thumb.png "Mountain landscape" --aspect 16:9
```

### Edit Existing Image

```bash
ORIG_CWD="$(pwd)" && cd ${CLAUDE_PLUGIN_ROOT} && uv run python main.py --cwd "$ORIG_CWD" output.png "Change the sky to sunset" --edit input.png
```

### With Reference Image

```bash
ORIG_CWD="$(pwd)" && cd ${CLAUDE_PLUGIN_ROOT} && uv run python main.py --cwd "$ORIG_CWD" output.png "Same style but with a car" --ref reference.png
```

### Multiple Variations

Generates numbered outputs (output_1.png, output_2.png, etc.):

```bash
ORIG_CWD="$(pwd)" && cd ${CLAUDE_PLUGIN_ROOT} && uv run python main.py --cwd "$ORIG_CWD" output.png "cat" "dog" "bird"
```

## Workflow

1. First use in context: Run setup commands (check_env.sh + uv sync)
2. Generate images as needed
3. Report output paths to user

## Notes

- Paid API tier recommended (free tier has strict rate limits)
- Output directories are created automatically
- Default aspect ratio is 16:9
