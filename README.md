# ai-skills

Agent skills I use, in the format Claude Code and Codex both read: a directory with
a `SKILL.md` that has YAML frontmatter, plus reference files the agent loads only
when it needs them.

## Skills

| Skill | What it does |
|---|---|
| [`guided-build`](skills/guided-build) | Instructor mode. The agent plans milestones and teaches one step per turn, and never writes the feature code. |

## Install

Both tools look for skills in a directory under their config, one subdirectory per
skill. Symlinking means a `git pull` updates every tool at once.

Clone once:

```sh
git clone https://github.com/romeuhcf/ai-skills.git ~/src/ai-skills
```

### Claude Code

```sh
mkdir -p ~/.claude/skills
ln -s ~/src/ai-skills/skills/guided-build ~/.claude/skills/guided-build
```

Invoke it by name:

```
/guided-build build a reverse proxy in Go
```

The agent also loads it on its own when a request matches the `description` in the
frontmatter, so "teach me X, no vibe coding" reaches it without the slash command.

If `CLAUDE_CONFIG_DIR` is set, use that path instead of `~/.claude`.

### Codex

Same layout, different directory:

```sh
mkdir -p ~/.codex/skills
ln -s ~/src/ai-skills/skills/guided-build ~/.codex/skills/guided-build
```

### Per project instead of per user

Both tools also read a `.claude/skills/` or `.codex/skills/` directory inside a
project, which is the way to share a skill with everyone working on that repo:

```sh
mkdir -p .claude/skills
cp -r ~/src/ai-skills/skills/guided-build .claude/skills/
```

Copy rather than symlink here, since the path has to resolve on someone else's
machine.

### Update

```sh
git -C ~/src/ai-skills pull
```

Symlinked installs pick it up immediately. Copied installs have to be copied again.

## Format

```
skills/<name>/
  SKILL.md          frontmatter: name, description. The instructions themselves.
  reference/*.md    loaded on demand, not with every session
```

The `description` is the only part the agent sees before deciding whether the skill
applies, so it carries the trigger phrases. Everything that is only needed once the
skill is running belongs in `SKILL.md` or, if it is long, in `reference/`.

## guided-build in one paragraph

You state a goal. The agent levels you with three probes, proposes milestones, and
then runs a loop: one question before you write anything, one change, one command
that proves it worked, and one question your passing code cannot answer for you. It
writes exactly one file, `LEARNING.md`, which tracks what you can now do and what
you got working without understanding. It does not write your code, and it does not
paste you a solution unless you ask for it outright, in which case it also gives you
the derivation and a variation to do yourself.

## Contributing

These are personal, so the repository takes no pull requests. Fork it and change
whatever you like.
