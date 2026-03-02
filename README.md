# Skills

Claude Code skills for AEC and workplace strategy by [ALPA](https://alpa.llc). MIT licensed.

## Available Skills

| Skill | Description |
|-------|-------------|
| [workplace-programmer](./workplace-programmer) | AI workplace strategy consultant that builds office space programs through conversation |
| [zoning](./zoning) | Zoning envelope analysis for lots in Maldonado, Uruguay using GIS data and TONE regulations |

## Installation

```bash
# Clone the repo
git clone https://github.com/AlpacaLabsLLC/skills.git

# Symlink a skill (recommended — stays in sync with updates)
ln -s "$(pwd)/skills/workplace-programmer" ~/.claude/skills/workplace-programmer
ln -s "$(pwd)/skills/zoning" ~/.claude/skills/zoning
```

Then use them in Claude Code:

```
/workplace-programmer 30,000 RSF tech company, 200 people
/zoning
```

See each skill's README for detailed usage and customization.

## License

MIT
