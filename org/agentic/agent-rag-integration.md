# RAG Integration Reference

## Decision Framework

### When to Use RAG
- **Yes**: Large docs (>100k tokens), frequently updated, domain-specific knowledge
- **Maybe**: Docs 10-100k tokens, team repeatedly references same sources
- **No**: Small docs (<10k tokens), one-off usage, already in LLM training data

### Progression Strategy
1. **Simple first**: Manual context, filesystem tools
2. **Leverage existing**: Community MCP servers, standard tools
3. **Build custom**: Only when existing solutions inadequate

---

## Approach 1: No RAG (Start Here)

### Manual Context Sharing
```bash
# Grab docs on-demand
curl -s https://docs.example.com/api | pbcopy
# Paste into AI conversation
```

### Filesystem MCP Server
```json
{
  "mcpServers": {
    "docs": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/docs"]
    }
  }
}
```
**Pros**: Zero setup, works immediately  
**Cons**: No semantic search, manual file selection

---

## Approach 2: Existing Solutions

### Community MCP Servers
Search first: https://github.com/punkpeye/awesome-mcp-servers

Common patterns:
- Project-specific: `mcp-postgres-docs`, `mcp-aws-docs`
- Generic: `mcp-context-server` (semantic search over directories)
- Hybrid: Filesystem + embedding capabilities

### Install Example
```json
{
  "mcpServers": {
    "project-docs": {
      "command": "npx",
      "args": ["-y", "@user/mcp-project-docs"],
      "env": {
        "DOCS_PATH": "/path/to/docs"
      }
    }
  }
}
```

**Pros**: Maintained, tested, often feature-rich  
**Cons**: May not fit exact needs, dependency on maintainer

---

## Approach 3: Custom RAG MCP Server

### Quick Stack
```
Docs → LangChain → OpenAI Embeddings → Local Chroma → MCP Tool
```

### Structure
```
mcp-project-docs/
├── src/
│   ├── index.ts          # MCP server
│   ├── rag.ts            # RAG logic
│   └── indexer.ts        # Doc preprocessing
├── data/
│   └── vector_store/     # Embedded docs
└── package.json
```

### Minimal Implementation
```typescript
// src/index.ts
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { searchDocs } from "./rag.js";

const server = new Server({
  name: "project-docs",
  version: "1.0.0"
}, { capabilities: { tools: {} } });

server.setRequestHandler(ListToolsRequestSchema, async () => ({
  tools: [{
    name: "search_docs",
    description: "Search project documentation",
    inputSchema: {
      type: "object",
      properties: {
        query: { type: "string", description: "Search query" },
        limit: { type: "number", default: 5 }
      },
      required: ["query"]
    }
  }]
}));

server.setRequestHandler(CallToolRequestSchema, async (request) => {
  if (request.params.name === "search_docs") {
    const { query, limit = 5 } = request.params.arguments;
    const results = await searchDocs(query, limit);
    return {
      content: [{ type: "text", text: results }]
    };
  }
});

const transport = new StdioServerTransport();
await server.connect(transport);
```

### RAG Component Options

#### Embeddings
- **OpenAI**: `text-embedding-3-small` (cheap, good)
- **Local**: `all-MiniLM-L6-v2` (free, fast, lower quality)
- **Cohere**: `embed-english-v3.0` (balanced)

#### Vector Store
- **Local**: Chroma, LanceDB, DuckDB with vss extension
- **Cloud**: Pinecone, Weaviate, Qdrant
- **Simple**: In-memory with cosine similarity

#### Chunking Strategy
```typescript
// For code documentation
const chunkConfig = {
  size: 1000,              // tokens
  overlap: 200,            // preserve context
  separators: ["\n## ", "\n### ", "\n\n", "\n"]  // respect structure
};
```

### Indexing Pipeline
```typescript
// src/indexer.ts
import { RecursiveCharacterTextSplitter } from "langchain/text_splitter";
import { OpenAIEmbeddings } from "langchain/embeddings/openai";
import { Chroma } from "langchain/vectorstores/chroma";

async function indexDocs(docsPath: string) {
  const splitter = new RecursiveCharacterTextSplitter({
    chunkSize: 1000,
    chunkOverlap: 200
  });
  
  const docs = await loadDocs(docsPath);
  const splits = await splitter.splitDocuments(docs);
  
  await Chroma.fromDocuments(
    splits,
    new OpenAIEmbeddings(),
    { collectionName: "project-docs" }
  );
}
```

---

## Integration Patterns

### On-Demand (Recommended)
Agent invokes RAG tool only when needed.
```typescript
// Agent decides: "I need DuckDB JOIN syntax"
await callTool("search_docs", { query: "JOIN syntax examples" });
```

### Auto-Context
Inject relevant docs based on file/error context.
```typescript
// If working on SQL file, pre-load SQL docs
if (file.endsWith('.sql')) {
  context += await searchDocs("SQL reference");
}
```

### Hybrid
Combine approaches based on signal strength.

---

## Production Considerations

### Performance
- **Cache embeddings**: Don't re-embed on every query
- **Lazy load**: Initialize vector store once
- **Batch**: Index docs in parallel

### Quality
- **Metadata**: Preserve doc structure (headings, sections)
- **Reranking**: Post-process results for relevance
- **Validation**: Test with real queries before deployment

### Maintenance
- **Update strategy**: Incremental vs full reindex
- **Version control**: Track doc versions with embeddings
- **Monitoring**: Log query quality, usage patterns

---

## Cost Estimates

### OpenAI Embeddings
- **Index**: ~$0.10 per 1M tokens (one-time)
- **Query**: ~$0.0001 per query
- **Example**: 10MB docs ≈ $0.05 to index

### Alternatives
- **Local models**: Free, slower, lower quality
- **Cohere**: Similar pricing, batch discounts

---

## Testing Your RAG

```bash
# Test queries that should work
echo '{"query": "how to create table"}' | node build/index.js

# Test edge cases
echo '{"query": "asdfghjkl"}' | node build/index.js

# Benchmark
time node scripts/benchmark.js --queries 100
```

---

## Quick Start Checklist

- [ ] Determine doc size, update frequency
- [ ] Search for existing MCP servers
- [ ] Try filesystem MCP + manual selection
- [ ] If needed, prototype custom RAG (LangChain + Chroma)
- [ ] Index subset of docs, test query quality
- [ ] Measure: relevance, latency, cost
- [ ] Deploy if metrics acceptable

---

## Resources

- **MCP Registry**: https://github.com/punkpeye/awesome-mcp-servers
- **MCP SDK**: https://github.com/modelcontextprotocol/typescript-sdk
- **LangChain Docs**: https://js.langchain.com/docs/
- **Llamaindex**: https://ts.llamaindex.ai/

---

## Common Pitfalls

❌ Building RAG when docs fit in context window  
❌ Over-engineering: complex reranking for simple use case  
❌ Ignoring chunk boundaries (breaking code examples)  
❌ No fallback when RAG returns poor results  
❌ Forgetting to update index when docs change  

✅ Start simple, add complexity only when needed  
✅ Measure quality before scaling  
✅ Keep human in loop for critical decisions
