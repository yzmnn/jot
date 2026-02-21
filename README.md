# Jot

> Personal AI assistant with persistent memory and knowledge graph

Jot is an AI-powered knowledge management system that builds a persistent knowledge graph from your Obsidian notes, remembers context across conversations, and automates routine knowledge work.

## Features

- **Persistent Memory**: Builds a Neo4j knowledge graph from your notes that compounds over time
- **Markdown Notes**: Works with any markdown files - simple and flexible
- **Context-Aware AI**: Uses Claude with RAG (graph + vector search) for intelligent responses
- **Local & Private**: All embeddings run locally with sentence-transformers (no data leaves your machine except Claude API calls)
- **Autonomous Agents**: Automatically summarizes notes and discovers connections
- **Beautiful CLI**: Rich terminal interface for queries and conversations

## Architecture

```
Markdown Notes → Parser → Entity Extraction (Claude) → Neo4j Graph
                                                       ↓
                                   Vector Embeddings (Local, Sentence Transformers)
                                                       ↓
User Query → Hybrid Retrieval (Graph + Vectors) → Claude RAG → Response
```

## Installation

### Prerequisites

1. **Python 3.11+**
2. **Neo4j** (Community Edition - Free)
   ```bash
   # macOS
   brew install neo4j
   neo4j start

   # Or use Docker
   docker run -d \
     --name neo4j \
     -p 7474:7474 -p 7687:7687 \
     -e NEO4J_AUTH=neo4j/your_password \
     neo4j:latest
   ```
3. **API Keys**
   - Anthropic API key (for Claude) - **ONLY required API key**

### Setup

```bash
# Clone the repo
git clone git@github.com:yzmnn/jot.git
cd jot

# Install dependencies
poetry install

# Add your API key to .env
# Edit .env and add your ANTHROPIC_API_KEY

# Configure your vault path
# Edit config.yaml and set your vault path and Neo4j password
```

## Usage

### Ingest Your Notes

```bash
# Scan your notes directory and build the knowledge graph
jot ingest ~/Documents/notes

# Or watch for changes in real-time
jot ingest ~/Documents/notes --watch
```

### Ask Questions

```bash
# One-off question
jot ask "What projects am I working on?"

# Interactive chat mode with persistent memory
jot chat
```

### Run Agents

```bash
# Generate summaries for all notes
jot summarize

# Discover connections between notes
jot link

# Show graph statistics
jot stats
```

## Configuration

Edit `config.yaml`:

```yaml
vault:
  path: "~/Documents/notes"
  watch: true

neo4j:
  uri: "bolt://localhost:7687"
  username: "neo4j"
  password: "your_password"

llm:
  provider: "anthropic"
  model: "claude-3-5-sonnet-20241022"
  api_key: "${ANTHROPIC_API_KEY}"

embeddings:
  provider: "sentence-transformers"  # Local, free, private
  model: "all-MiniLM-L6-v2"
```

## Project Structure

```
jot/
├── src/jot/
│   ├── ingest/          # Markdown parser
│   ├── extraction/      # Entity extraction with Claude
│   ├── graph/           # Neo4j knowledge graph
│   ├── vectorstore/     # Local embeddings (sentence-transformers)
│   ├── rag/             # Hybrid retrieval + RAG
│   ├── memory/          # Conversation memory
│   ├── agents/          # Autonomous agents
│   └── cli/             # CLI interface
├── tests/
├── data/                # Vector store, cache
├── config.yaml
└── pyproject.toml
```

## How It Works

### 1. Ingestion
- Scans your notes directory for `.md` files
- Parses markdown content
- Extracts entities (people, projects, concepts) using Claude
- Builds knowledge graph in Neo4j
- Creates vector embeddings locally (free, private)

### 2. Knowledge Graph
Stores structured knowledge as nodes and relationships:

**Nodes**: Note, Person, Project, Concept, Task
**Relationships**: LINKS_TO, MENTIONS, RELATED_TO, WORKS_ON, etc.

### 3. Hybrid Retrieval
When you ask a question:
1. Vector search finds semantically similar content (runs locally)
2. Graph traversal explores connected knowledge (Neo4j)
3. Combines both for comprehensive context
4. Claude generates response with full context

### 4. Agents
Background automation:
- **Summarizer**: Creates AI summaries of notes
- **Linker**: Discovers non-obvious connections
- **Context Helper**: Prepares briefings for tasks

## Privacy

- **Embeddings**: Run 100% locally with sentence-transformers (your notes never leave your machine for vector search)
- **Graph**: Stored locally in Neo4j
- **Claude API**: Only used for entity extraction and question answering (you control what gets sent)

## Development

```bash
# Run tests
poetry run pytest

# Format code
poetry run black src/ tests/

# Type checking
poetry run mypy src/

# Linting
poetry run ruff check src/
```

## Roadmap

- [x] Phase 1: Project setup
- [ ] Phase 2: Obsidian parser + Neo4j graph
- [ ] Phase 3: Entity extraction + vector search
- [ ] Phase 4: Conversation memory
- [ ] Phase 5: Autonomous agents
- [ ] Phase 6: Graph visualization
- [ ] Phase 7: Voice memo support

## License

MIT

## Acknowledgments

Inspired by [Rowboat](https://github.com/rowboatlabs/rowboat)