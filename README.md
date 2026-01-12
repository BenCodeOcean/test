# Claude Skills Collection

Private collection of Claude Code skills organized by category.

## Categories

### DevOps (`devops/`)
Skills for deployment, infrastructure, and operations automation.

- **codeocean-deployment**: Deploy and manage Code Ocean environments in AWS accounts

### Examples (`examples/`)
Simple example skills for learning and testing.

- **hello-world**: Print a colorful hello world message

## Installation

**Install all skills:**
```bash
claude skill install https://github.com/BenCodeOcean/test.git
```

**Install by category:**
```bash
# DevOps skills
claude skill install https://github.com/BenCodeOcean/test.git/devops

# Example skills
claude skill install https://github.com/BenCodeOcean/test.git/examples
```

**Install specific skill:**
```bash
# CodeOcean deployment
claude skill install https://github.com/BenCodeOcean/test.git/devops/codeocean-deployment

# Hello World
claude skill install https://github.com/BenCodeOcean/test.git/examples/hello-world
```

## Adding New Skills

To add a new skill:

1. Choose the appropriate category folder (or create a new one)
2. Create a new folder with your skill name
3. Add a `SKILL.md` file with the skill definition
4. Update this README and the category README

## Skill Structure

Each skill should have:
- `SKILL.md` - Required: skill definition with frontmatter and instructions
- `README.md` - Optional: additional documentation

## Private Repository

This is a private repository. Users need appropriate git access (SSH keys or PAT) to install these skills.
