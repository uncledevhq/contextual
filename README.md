# Contextual

A repository of API context documentation for popular APIs. These context files are designed to be copied and added to LLMs, Cursor, or AI text editors to provide comprehensive API knowledge and improve code generation and assistance.

## Purpose

This repository contains structured, comprehensive context documentation for various APIs. Each context file includes:
- API overview and capabilities
- Authentication methods
- Base URLs and endpoints
- Request/response formats
- Code examples in multiple languages
- Best practices
- Error handling
- Support resources

## Usage

1. Browse the `apis/` directory for available API contexts
2. Copy the content of the relevant API context file
3. Add it to your:
   - **Cursor**: Add to your `.cursorrules` or context window
   - **LLM Chat**: Paste into the conversation for context
   - **AI Text Editor**: Add to your project documentation or context

## Available APIs

- [Lenco API](./apis/lenco.md) - Financial services API for disbursements, virtual accounts, and bill payments

## Contributing

When adding a new API context:
1. Create a new markdown file in the `apis/` directory
2. Follow the structure of existing context files
3. Include comprehensive documentation with examples
4. Update this README with the new API entry

## Structure

```
contextual/
├── README.md
└── apis/
    ├── lenco.md
    └── [other-api].md
```

## License

This repository contains documentation and context information for educational and development purposes.
