# Codex skills

Reusable skills for [OpenAI Codex](https://developers.openai.com/codex/skills/).

## Available skills

- **delegate** — orchestrate substantial engineering work through grounded,
  independently verified subagent slices.
- **untangle-codebase** — restructure tangled code into cohesive, low-coupling
  modules while preserving observable behavior.

## Install with Codex

Ask Codex:

> Install `delegate` and `untangle-codebase` from
> `https://github.com/cmihai/skills`.

The equivalent skill-installer invocation is:

```sh
install-skill-from-github.py \
  --repo cmihai/skills \
  --path skills/delegate skills/untangle-codebase
```

To install one skill, provide its GitHub directory URL:

```text
https://github.com/cmihai/skills/tree/main/skills/delegate
```

The installer copies each selected directory to `~/.codex/skills` and names
the installed skill after the directory basename. It aborts if that destination
already exists; to update a skill, remove or rename the existing installed copy
before reinstalling it. A newly installed skill is available on the next Codex
turn.

Each directory under [`skills/`](skills/) is self-contained, with `SKILL.md` at
its root and all referenced resources beneath it. Stable releases may be
installed with `--ref <tag>`; omitting `--ref` installs from `main`.

## Manual development install

For local development, clone the repository and copy or symlink individual
skill directories:

```sh
git clone https://github.com/cmihai/skills.git
mkdir -p ~/.codex/skills
cp -R skills/delegate skills/untangle-codebase ~/.codex/skills/
```

## License

[0BSD](LICENSE)
