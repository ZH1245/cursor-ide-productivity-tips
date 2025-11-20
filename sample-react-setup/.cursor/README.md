# Cursor Configuration

This folder contains Cursor AI configuration files following the [Cursor Rules format](https://cursor.com/docs/context/rules).

## Structure

```
.cursor/
├── rules/              # Project Rules (MDC format)
│   ├── react-nextjs-standards.mdc
│   ├── documentation-standards.mdc
│   ├── internationalization.mdc
│   ├── component-patterns.mdc
│   ├── seo-optimization.mdc
│   ├── typescript-standards.mdc
│   ├── error-handling.mdc
│   ├── performance-optimization.mdc
│   ├── testing-standards.mdc
│   └── commit-standards.mdc
└── cli.json            # CLI permissions configuration
```

## Rule Types

### Always Apply Rules
- `react-nextjs-standards.mdc` - Core React/Next.js patterns
- `documentation-standards.mdc` - JSDoc requirements
- `commit-standards.mdc` - Git commit conventions

### File-Specific Rules
- `internationalization.mdc` - Applies to i18n files
- `component-patterns.mdc` - Applies to components
- `seo-optimization.mdc` - Applies to pages/layouts
- `typescript-standards.mdc` - Applies to TypeScript files
- `error-handling.mdc` - Applies to API/services
- `performance-optimization.mdc` - Applies to components/hooks
- `testing-standards.mdc` - Applies to test files

## Nested Rules

Subdirectories can have their own `.cursor/rules/` for domain-specific patterns:
- `app/components/.cursor/rules/` - Component-specific templates

## How It Works

Cursor automatically reads and applies rules based on:
1. **alwaysApply: true** - Applied to every chat session
2. **globs** - Applied when matching files are referenced
3. **description** - Used for intelligent application when relevant

## Updating Rules

1. Edit rule files in `.cursor/rules/` for project-specific patterns
2. Edit `AGENTS.md` in project root for general guidelines
3. Rules are automatically indexed and applied by Cursor

## Best Practices

- Keep rules focused and under 500 lines
- Use specific globs for file-specific rules
- Provide concrete examples in rules
- Reference actual files using `@filename.ts` syntax
