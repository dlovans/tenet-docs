# Tenet Documentation Website

The official documentation website for **Tenet** — a declarative logic VM for JSON schemas.

## About Tenet

Tenet is a logic engine that processes JSON documents according to rules you define. It can:

- Validate data against constraints
- Show/hide fields based on conditions
- Calculate values automatically
- Enforce signature requirements
- Apply different rules based on dates

**What Tenet is NOT:**
- Not a database (it doesn't store data)
- Not a signing service (it validates signatures, not creates them)
- Not a blockchain

## Documentation

Visit the live docs: **[tenet.dev](https://tenet.dev)** *(or your deployed URL)*

### Sections

- **Introduction** — What Tenet is and how it works
- **Schema Reference** — Full definition syntax
- **Operators** — JSON-logic operators
- **API Reference** — Go, JavaScript, and CLI usage
- **Examples** — Real-world use cases
- **Temporal Routing** — Date-based rule versioning
- **Verification** — Audit trail algorithm

## Development

```bash
# Install dependencies
npm install

# Start dev server
npm run dev

# Build for production
npm run build
```

## Tech Stack

- **SvelteKit 5** with Svelte 5 runes
- **Tailwind CSS 4**
- **MDSvex** for markdown documentation
- **Lucide** icons

## Links

- [Tenet VM Repository](https://github.com/dlovans/tenet)
- [Documentation Source](https://github.com/dlovans/tenet-docs)

## License

MIT
