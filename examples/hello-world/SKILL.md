---
name: hello-world
description: Print a colorful hello world message. Use when user asks for a hello world example or wants to test colorful output.
version: 1.0.0
context: fork
agent: general-purpose
allowed-tools:
  - Bash
---

# Hello World Skill

This skill demonstrates how to create a simple Claude Code skill that prints colorful output.

## Use Cases

- Testing skill installation
- Learning skill development
- Demonstrating colorful terminal output
- Quick verification that skills are working

## What It Does

When invoked, this skill prints "Hello World" in various colors using ANSI escape codes.

## Instructions

When this skill is triggered, execute the following:

1. Print a colorful hello world message using bash with ANSI color codes
2. Display the message in multiple colors to demonstrate terminal color support

Use this bash command to print colorful output:

```bash
echo -e "\033[1;31m█████\033[0m \033[1;33m█████\033[0m \033[1;32m█████\033[0m \033[1;36m█████\033[0m \033[1;34m█████\033[0m \033[1;35m█████\033[0m"
echo -e "\033[1;31m█\033[0m   \033[1;31m█\033[0m \033[1;33m█\033[0m     \033[1;32m█\033[0m     \033[1;36m█\033[0m     \033[1;34m█\033[0m   \033[1;34m█\033[0m"
echo -e "\033[1;31m█████\033[0m \033[1;33m█████\033[0m \033[1;32m█\033[0m     \033[1;36m█\033[0m     \033[1;34m█\033[0m   \033[1;34m█\033[0m"
echo -e "\033[1;31m█\033[0m   \033[1;31m█\033[0m \033[1;33m█\033[0m     \033[1;32m█\033[0m     \033[1;36m█\033[0m     \033[1;34m█\033[0m   \033[1;34m█\033[0m"
echo -e "\033[1;31m█\033[0m   \033[1;31m█\033[0m \033[1;33m█████\033[0m \033[1;32m█████\033[0m \033[1;36m█████\033[0m \033[1;34m█████\033[0m"
echo ""
echo -e "\033[1;36m█\033[0m   \033[1;36m█\033[0m \033[1;35m█████\033[0m \033[1;31m█████\033[0m \033[1;33m█\033[0m     \033[1;32m█████\033[0m"
echo -e "\033[1;36m█\033[0m   \033[1;36m█\033[0m \033[1;35m█\033[0m   \033[1;35m█\033[0m \033[1;31m█\033[0m   \033[1;31m█\033[0m \033[1;33m█\033[0m     \033[1;32m█\033[0m    "
echo -e "\033[1;36m█\033[0m █ \033[1;36m█\033[0m \033[1;35m█\033[0m   \033[1;35m█\033[0m \033[1;31m█████\033[0m \033[1;33m█\033[0m     \033[1;32m█\033[0m    "
echo -e "\033[1;36m█\033[0m █ \033[1;36m█\033[0m \033[1;35m█\033[0m   \033[1;35m█\033[0m \033[1;31m█\033[0m   \033[1;31m█\033[0m \033[1;33m█\033[0m     \033[1;32m█\033[0m    "
echo -e "\033[1;36m█████\033[0m \033[1;35m█████\033[0m \033[1;31m█\033[0m   \033[1;31m█\033[0m \033[1;33m█████\033[0m \033[1;32m█████\033[0m"
echo ""
echo -e "\033[1;37m✨ Welcome to Claude Code Skills! ✨\033[0m"
```

After printing, add a friendly message explaining that the skill executed successfully.

## Example Output

The skill will produce colorful ASCII art spelling "HELLO WORLD" followed by a welcome message.

## Notes

- Uses standard ANSI escape codes that work in most terminals
- Safe to run in any environment
- No external dependencies required
