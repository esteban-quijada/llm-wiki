# llm-wiki

A CLI tool that turns URLs into a local Obsidian wiki using [fabric-ai](https://github.com/danielmiessler/fabric). Submit a web page, YouTube video, or PDF and get structured, cross-linked markdown study notes automatically.

Inspired by [Karpathy's LLM wiki architecture](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) and [pin-llm-wiki](https://github.com/ndjordjevic/pin-llm-wiki).

## How it works

```
URL --> fabric-ai fetches content --> wiki_generate pattern splits into topics
    --> pages written to Obsidian vault with [[wikilinks]]
    --> index.md auto-updated --> log.md entry added
    --> raw content archived
```

- **Web pages** are scraped via fabric-ai's Jina AI integration (`-u`)
- **YouTube videos** are transcribed via `yt-dlp` (`-y`)
- **PDFs** are extracted via `pdftotext` and piped to fabric-ai
- A custom fabric pattern (`wiki_generate`) instructs the LLM to split content into topic pages with cross-references
- All output is Obsidian-compatible markdown with `[[wikilinks]]`

## Dependencies

| Dependency | Required | Install |
|---|---|---|
| fabric-ai | Yes | `brew install fabric-ai` |
| python3 | Yes | Pre-installed on macOS |
| curl | Yes | Pre-installed on macOS |
| poppler (pdftotext) | For PDFs | `brew install poppler` |

fabric-ai must be configured with at least one LLM provider. Run `fabric-ai --setup` if you haven't already.

## Install

```bash
git clone https://github.com/your-username/llm-wiki.git
cd llm-wiki
./bin/wiki-add --setup
```

The setup wizard will:
1. Prompt for your vault name, topics directory, fabric pattern, and model
2. Create `config.env` and the directory structure
3. Install the bundled `wiki_generate` fabric pattern into `~/.config/fabric/patterns/`
4. Check that all dependencies are available

## Usage

```bash
# Add a web page
wiki-add -c system-design "https://www.hellointerview.com/learn/system-design/core-concepts/networking-essentials"

# Add a YouTube video
wiki-add -c ccna "https://www.youtube.com/watch?v=..."

# Add a PDF
wiki-add -c ccna "https://example.com/study-plan.pdf"

# Change settings
wiki-add --config
```

If you omit `-c`, the tool will prompt for a category interactively.

## Repo structure

```
llm-wiki/
├── bin/wiki-add                        # CLI tool
├── patterns/wiki_generate/system.md    # Bundled fabric pattern
├── .gitignore
└── README.md
```

After setup, user-specific files are created (gitignored):

```
├── config.env                          # Your settings
├── raw/                                # Archived source content
└── <vault-name>/                       # Obsidian vault
    ├── index.md                        # Auto-generated topic index
    ├── log.md                          # Activity log
    └── topics/                         # Generated wiki pages
```

## Customization

### Changing the fabric pattern

The `wiki_generate` pattern at `patterns/wiki_generate/system.md` controls how pages are structured. Edit it to change the page template, splitting behavior, or wikilink style. Run `wiki-add --setup` again to reinstall it.

### Using a different LLM model

```bash
wiki-add --config
# Select option 4 to set a model (e.g., claude-sonnet-4-20250514, gpt-4o)
```

Or set `FABRIC_MODEL` directly in `config.env`.
