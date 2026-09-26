# AI Skills

A public collection of skills for AI assistants. Each skill provides specialized instructions that extend your agent's capabilities for specific tasks.

## Available skills

### `test-engineering`

Guidance for writing and validating tests that provide trustworthy evidence without unnecessary maintenance cost.

**Contents:**

- Test-driven development and regression proof.
- Choosing the right boundary and avoiding duplicate coverage.
- Evaluating test sensitivity, reliability, and cost.
- When to use property, fuzz, mutation, and other testing techniques.

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
npx skills add alereyleyva/skills --skill test-engineering
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

Once installed, your agent can invoke the skills when appropriate. Use `test-engineering` when writing, changing, reviewing, validating, or auditing tests, and when tests are a quality gate for behavior changes. Use `implementation-brief` at the end of a technical implementation task, after completing the expected checks. The brief is written in English, regardless of the conversation language.

## Repository structure

Each directory contains a skill and its `SKILL.md` file:

```text
test-engineering/
└── SKILL.md
implementation-brief/
└── SKILL.md
```

## Contributing

Contributions are welcome. Add each skill in its own directory and include a `SKILL.md` with the skill's name, a clear description, and usage instructions.

## License

This project is licensed under the [MIT License](LICENSE).
