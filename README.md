# AI Skills

A public collection of skills for AI assistants. Each skill provides specialized instructions that extend your agent's capabilities for specific tasks.

## Available skills

### `implementation-brief`

Creates a concise technical brief at the end of an implementation task, based on the actual changes and checks performed.

**Contents:**

- When to generate a brief and which tasks it covers.
- How to review the request, final diff, and checks before writing.
- Which changes, decisions, deviations, and caveats are worth preserving.
- How to describe tests without claiming results that were not verified.
- Brief format: status, changes, significance, and checks.
- Style, brevity, and content to omit.

## Installation

This repository uses the [Skills CLI](https://skills.sh), which installs skills for compatible agents.

Install a specific skill:

```bash
npx skills add alereyleyva/skills --skill implementation-brief
```

List the skills available in this repository:

```bash
npx skills add alereyleyva/skills --list
```

Browse and install interactively:

```bash
npx skills add alereyleyva/skills
```

The CLI lets you choose skills and target agents. See `npx skills add --help` for options such as non-interactive installation or selecting an agent. After installation, follow your agent's instructions for loading the skill; you may need to restart the session.

## Usage

Once installed, your agent can invoke the skill when appropriate. Use `implementation-brief` at the end of a technical implementation task, after completing the expected checks. The skill writes the brief in English, regardless of the conversation language.

## Repository structure

Each directory contains a skill and its `SKILL.md` file:

```text
implementation-brief/
└── SKILL.md
```

## Contributing

Contributions are welcome. Add each skill in its own directory and include a `SKILL.md` with the skill's name, a clear description, and usage instructions.

## License

This project is licensed under the [MIT License](LICENSE).
