# Codex skills

Reusable skills for [OpenAI Codex](https://developers.openai.com/codex/skills/).

## Available skills

- **delegate** — orchestrate substantial engineering work through grounded,
  independently verified subagent slices.
- **untangle-codebase** — restructure tangled code into cohesive, low-coupling
  modules while preserving observable behavior.

## Install

Clone the repository, then copy or symlink the skill directories into your
personal Codex skills directory:

```sh
git clone https://github.com/cmihai/skills.git
mkdir -p ~/.codex/skills
cp -R skills/skills/delegate skills/skills/untangle-codebase ~/.codex/skills/
```

Restart Codex after installation so it discovers the skills.

Each directory under [`skills/`](skills/) is self-contained. You can install
only the skills you want.

## License

[0BSD](LICENSE)
