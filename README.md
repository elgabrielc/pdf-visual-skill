# pdf-visual

A Claude Code skill that automatically reads PDFs visually when text parsing fails.

## The Problem

Claude Code's `WebFetch` tool often fails to extract readable content from PDFs, especially:
- Image-based or scanned PDFs
- Graphics-heavy documents (created in Illustrator, etc.)
- PDFs with complex layouts

When this happens, you get garbled text or empty results.

## The Solution

This skill downloads the PDF and uses Claude's multimodal capabilities to read it visually, extracting the actual content from the rendered pages.

## Installation

Copy the skill to your Claude Code skills directory:

```bash
# Create the skills directory if it doesn't exist
mkdir -p ~/.claude/skills/pdf-visual

# Copy the skill file
cp SKILL.md ~/.claude/skills/pdf-visual/SKILL.md
```

Or clone directly:

```bash
git clone https://github.com/elgabrielc/pdf-visual-skill.git
mkdir -p ~/.claude/skills
cp -r pdf-visual-skill/SKILL.md ~/.claude/skills/pdf-visual/
```

## Usage

### Automatic Invocation

The skill triggers automatically when PDF parsing fails. Just ask Claude to fetch a PDF:

```
Fetch this PDF and tell me what it says: https://example.com/document.pdf
```

If `WebFetch` fails to parse it, Claude will automatically invoke the skill.

### Manual Invocation

```bash
# Basic usage
/pdf-visual https://example.com/document.pdf

# With a specific question
/pdf-visual https://example.com/manual.pdf "What are the installation steps?"

# Local file
/pdf-visual /path/to/local/file.pdf

# Local file with question
/pdf-visual /path/to/document.pdf "Summarize the key findings"
```

## Features

- **Automatic detection**: Triggers when WebFetch fails on PDFs
- **HTTP validation**: Checks status codes and file types before reading
- **Error handling**: Reports download failures, invalid files, and suggests fixes
- **Smart cleanup**: Deletes temp files after successful reads, keeps them on errors
- **Question support**: Pass an optional prompt to focus on specific content

## How It Works

1. Downloads the PDF to a temporary scratchpad directory
2. Validates the download (HTTP status, file type check)
3. Uses Claude's Read tool to visually inspect the PDF
4. Extracts and summarizes the content
5. Cleans up the temporary file

## Requirements

- Claude Code CLI
- `curl` (for downloading)
- `file` command (for validation)

## License

MIT License - see [LICENSE](LICENSE)

## Contributing

Issues and PRs welcome.

## Author

Gabriel Casalduc - Divergent Health Technologies
