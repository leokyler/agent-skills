# Agent Skills

This repository contains agent skills for various tasks.

## Skills

### minimax-lyrics

A skill for generating song lyrics using the Minimax API. Supports full song creation and lyrics editing/continuation.

**Features:**
- Write complete songs based on theme, style, or mood
- Edit and continue existing lyrics
- Multiple output formats (txt, lrc, md)

**Requirements:**
- `MINIMAX_API_KEY` environment variable (get it from [Minimax Platform](https://platform.minimaxi.com/user-center/basic-information/interface-key))

**Usage:**

```bash
# Set your API key
export MINIMAX_API_KEY=your_api_key_here
```

Then trigger the skill by asking to write a song, generate lyrics, or continue/edit lyrics.

## License

MIT