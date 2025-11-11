# Contextual

A curated repository of comprehensive API context documentation for popular APIs. These context files are designed to be copied and added to LLMs, Cursor, or AI text editors to provide detailed API knowledge and significantly improve code generation and AI assistance.

## 🎯 Purpose

When working with AI coding assistants (like Cursor, GitHub Copilot, ChatGPT, etc.), providing context about the APIs you're using dramatically improves the quality of generated code. This repository aims to be a central hub for well-structured, comprehensive API context documentation that developers can easily copy and use.

**Why use this?**
- ✅ Save time by not having to write API context from scratch
- ✅ Ensure consistency across your team's AI-assisted development
- ✅ Get better code suggestions with comprehensive API knowledge
- ✅ Share and maintain API context documentation as a community

## 📖 What's Included

Each API context file in this repository includes:

- **API Overview** - What the API does and its key capabilities
- **Base URLs** - Production and sandbox endpoints
- **Authentication** - How to authenticate requests (API keys, OAuth, etc.)
- **Response Format** - Standard response structure
- **HTTP Status Codes** - What each status code means
- **Error Codes** - Detailed error code reference
- **Key Endpoints** - All major endpoints with examples
- **Code Examples** - Working examples in multiple languages (JavaScript, Python, cURL)
- **TypeScript Types** - Type definitions when applicable
- **Webhooks** - Webhook setup and verification
- **Test Data** - Sandbox/test credentials and data
- **Best Practices** - Security, error handling, and optimization tips
- **Common Pitfalls** - What to avoid when integrating
- **Support Resources** - Documentation links and contact information

## 🚀 Usage

### For Cursor Users

1. Open the API context file you need (e.g., `apis/lenco.md`)
2. Copy the entire content
3. Add it to your project in one of these ways:
   - **Option A**: Add to `.cursorrules` file in your project root
   - **Option B**: Use Cursor's context panel and paste the content
   - **Option C**: Create a `docs/` folder and reference it in your project

### For LLM Chat (ChatGPT, Claude, etc.)

1. Open the API context file you need
2. Copy the entire content
3. At the start of your conversation, paste it with a message like:
   ```
   Here's the context for the [API Name] API. Use this information 
   to help me with my integration:
   
   [paste context here]
   ```

### For AI Text Editors

1. Copy the relevant API context
2. Add it to your project documentation
3. Reference it in your editor's context settings
4. Or include it in your project's README or docs folder

### Quick Example

```bash
# Copy Lenco API context
cat apis/lenco.md | pbcopy  # macOS
cat apis/lenco.md | xclip   # Linux

# Then paste into your Cursor context or LLM chat
```

## 📚 Available APIs

- **[Lenco API](./apis/lenco.md)** - Financial services API for payment processing in Zambia, supporting Mobile Money and Bank transfers

*More APIs coming soon!*

## 🤝 Contributing

We welcome contributions! Adding API context documentation helps the entire developer community.

### How to Contribute

1. **Fork the repository**
2. **Create a new API context file** in the `apis/` directory
   - Use lowercase, hyphenated filename (e.g., `stripe.md`, `twilio.md`)
   - Follow the structure of existing files (see `apis/lenco.md` as a reference)
3. **Update this README** to add your API to the "Available APIs" section
4. **Submit a pull request** with a clear description

### Contribution Guidelines

#### File Structure

Each API context file should follow this structure:

```markdown
# [API Name] API Integration - Cursor Context

## Project Overview
[Brief description of what the API does]

## API Base URLs
[Production and sandbox URLs]

## Authentication
[How to authenticate, get API keys, security notes]

## Response Format
[Standard response structure]

## HTTP Status Codes
[Status codes and their meanings]

## Error Codes
[Error code reference table]

## Key Endpoints
[All major endpoints with examples]

## Code Examples
[Examples in JavaScript, Python, cURL]

## Webhooks
[Webhook setup and verification]

## Test Data
[Sandbox/test credentials]

## Best Practices
[Security, error handling, optimization]

## Common Pitfalls to Avoid
[What to avoid]

## Support
[Documentation links, support contacts]
```

#### Quality Standards

- ✅ **Comprehensive**: Include all essential information developers need
- ✅ **Accurate**: Verify all endpoints, codes, and examples are correct
- ✅ **Up-to-date**: Use the latest API documentation
- ✅ **Well-formatted**: Use proper markdown formatting
- ✅ **Code Examples**: Include working examples in multiple languages
- ✅ **Practical**: Focus on real-world usage patterns

#### What to Include

- [ ] API overview and capabilities
- [ ] Base URLs (production and sandbox)
- [ ] Authentication method and setup
- [ ] Response format structure
- [ ] HTTP status codes
- [ ] Error codes (if applicable)
- [ ] All major endpoints with request/response examples
- [ ] Code examples (JavaScript/Node.js, Python, cURL minimum)
- [ ] TypeScript types (if applicable)
- [ ] Webhook setup and verification
- [ ] Test data and sandbox credentials
- [ ] Best practices and security notes
- [ ] Common pitfalls to avoid
- [ ] Support resources and documentation links

#### What NOT to Include

- ❌ API keys or secrets (use placeholders)
- ❌ Outdated or deprecated endpoints
- ❌ Proprietary or internal-only information
- ❌ Incomplete or placeholder content

### Review Process

1. Your PR will be reviewed for:
   - Completeness and accuracy
   - Code example correctness
   - Formatting consistency
   - Adherence to guidelines

2. Feedback will be provided if changes are needed

3. Once approved, your contribution will be merged!

### Suggesting APIs

Don't have time to write a full context file? Open an issue to suggest APIs you'd like to see added. Include:
- API name
- Link to official documentation
- Why it would be useful

## 📁 Repository Structure

```
contextual/
├── README.md           # This file
└── apis/               # API context files
    ├── lenco.md        # Lenco API context
    └── [api-name].md   # Other API contexts
```

## 🎨 Style Guide

- Use clear, descriptive headings
- Include code examples with syntax highlighting
- Use tables for error codes and reference data
- Add comments in code examples explaining key points
- Keep language consistent and professional
- Use emojis sparingly (only in README, not in API docs)

## 🔄 Keeping Context Files Updated

API documentation changes over time. If you notice outdated information:

1. Open an issue describing what's outdated
2. Or submit a PR with the corrections
3. Include a link to the updated official documentation

## 📝 License

This repository contains documentation and context information for educational and development purposes. All content is provided as-is to help developers integrate with various APIs.

## 🙏 Acknowledgments

Thank you to all contributors who help make this repository a valuable resource for the developer community!

## 📞 Support

- **Issues**: Open an issue for bugs, suggestions, or questions
- **Discussions**: Use GitHub Discussions for general questions
- **Contributions**: See [Contributing](#-contributing) section above

---

**Made with ❤️ for developers using AI coding assistants**
