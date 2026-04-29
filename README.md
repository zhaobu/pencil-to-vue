# pencil-to-vue

A [Cursor](https://cursor.com) Agent Skill for converting [Pencil](https://pencil.dev) (`.pen`) design files into production-quality **Vue 3 + TypeScript + Element Plus** code.

## Features

- **8 critical rules** enforcing design system reuse, variable-driven styling, overflow prevention, visual verification, and section-by-section development
- **Design token extraction** — auto-sync Pencil variables to SCSS CSS custom properties
- **Component mapping** — Pencil components to Element Plus (13 mappings)
- **Layout, typography, and icon mapping** — complete Pencil-to-CSS reference tables
- **Visual verification workflow** — `get_screenshot` + `snapshot_layout` after each section
- **Reference patterns** for common Vue page types:
  - Search + Table + Pagination pages
  - Form dialog components
  - Dashboard cards and charts (ECharts)

## Installation

### Personal (all projects)

```bash
git clone https://github.com/zhaobu/pencil-to-vue.git ~/.cursor/skills/pencil-to-vue
```

### Project-specific

```bash
git clone https://github.com/zhaobu/pencil-to-vue.git .cursor/skills/pencil-to-vue
```

## Target Stack

| Layer | Technology |
|-------|-----------|
| Framework | Vue 3 Composition API |
| Language | TypeScript |
| UI Library | Element Plus |
| Styling | SCSS + CSS Custom Properties |
| Icons | Element Plus Icons / Material Symbols |
| Charts | ECharts via vue-echarts |
| State | Pinia |
| Router | vue-router 4 |

## Usage

The skill activates automatically when you:

- Generate Vue code from `.pen` design files
- Sync Pencil design tokens to SCSS variables
- Build pages/components matching a Pencil prototype
- Mention "pencil to vue", "prototype to code", or "design to code"

## File Structure

```
pencil-to-vue/
├── SKILL.md                          # Main skill instructions (8 rules + workflow)
├── README.md
└── references/
    ├── page-patterns.md              # Search + Table + Pagination template
    ├── dialog-patterns.md            # Form dialog template
    └── dashboard-patterns.md         # Dashboard cards & charts template
```

## Contributing

Contributions are welcome! Please open an issue or submit a pull request.

## License

MIT
