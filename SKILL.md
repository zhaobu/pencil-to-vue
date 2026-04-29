---
name: pencil-to-vue
description: >
  Convert Pencil (.pen) design files to Vue 3 + TypeScript + Element Plus production code.
  Enforces design system reuse, variable-driven styling, layout correctness, and visual verification.
  Use when generating Vue code from .pen designs, syncing design tokens to SCSS/CSS variables,
  or building Vue SFC pages/components that match a Pencil prototype.
  Also triggers on "pencil to vue", "prototype to code", "design to code", "pen file",
  or any work involving .pen files in a Vue project.
---

# Pencil to Vue

Convert Pencil (.pen) designs into production-quality Vue 3 + TypeScript code while maintaining pixel-perfect fidelity to the original design.

## When to Use

- Generating Vue SFC code from `.pen` design files
- Syncing Pencil design tokens to SCSS/CSS custom properties
- Building pages or components that match a Pencil prototype
- Auditing existing Vue code against a Pencil design
- Importing existing Vue code structure back into Pencil designs

## Target Stack

| Layer | Technology |
|-------|-----------|
| Framework | Vue 3 Composition API (`<script setup lang="ts">`) |
| Language | TypeScript |
| UI Library | Element Plus (or project's existing library) |
| Styling | SCSS with CSS custom properties (`var(--xxx)`) |
| Icons | Element Plus Icons / Material Symbols (per project) |
| Charts | ECharts via vue-echarts |
| State | Pinia (for cross-component state) |
| Router | vue-router 4 |

## Critical Rules

### Rule 1: Reuse Design System Components

Before creating any element, check the `.pen` file for existing reusable components:

```
pencil_batch_get({ patterns: [{ reusable: true }], readDepth: 3 })
```

Map Pencil reusable components to Element Plus equivalents:

| Pencil Component | Element Plus | Vue Usage |
|-----------------|-------------|-----------|
| Button | `<el-button>` | `<el-button type="primary">` |
| Card / Panel | `<el-card>` | `<el-card shadow="never">` |
| Input / TextField | `<el-input>` | `<el-input v-model="value">` |
| Select / Dropdown | `<el-select>` | `<el-select v-model="value">` |
| Table / DataTable | `<el-table>` | `<el-table :data="list">` |
| Dialog / Modal | `<el-dialog>` | `<el-dialog v-model="visible">` |
| Tag / Badge | `<el-tag>` | `<el-tag type="success">` |
| Form | `<el-form>` | `<el-form :model="form" :rules="rules">` |
| Tabs | `<el-tabs>` | `<el-tabs v-model="activeTab">` |
| Pagination | `<el-pagination>` | See pagination pattern below |
| Progress | `<el-progress>` | `<el-progress :percentage="50">` |
| Descriptions | `<el-descriptions>` | `<el-descriptions :column="2" border>` |
| Timeline | `<el-timeline>` | `<el-timeline>` |

Only create custom components when no Element Plus equivalent exists.

### Rule 2: Use Variables, Never Hardcode

Before applying any style, read design tokens:

```
pencil_get_variables({ filePath: "path/to/file.pen" })
```

Map Pencil variables to SCSS CSS custom properties:

| Pencil Variable | CSS Custom Property | Usage |
|----------------|--------------------|----|
| `primary` | `--el-color-primary` | `var(--el-color-primary)` |
| `background` / `bg-page` | `--bg-page` | `var(--bg-page)` |
| `foreground` / `text-primary` | `--text-primary` | `var(--text-primary)` |
| `muted-foreground` | `--text-muted` | `var(--text-muted)` |
| `sidebar-bg` | `--bg-sidebar` | `var(--bg-sidebar)` |
| `success` | `--el-color-success` | `var(--el-color-success)` |
| `danger` / `error` | `--el-color-danger` | `var(--el-color-danger)` |
| `warning` | `--el-color-warning` | `var(--el-color-warning)` |
| `radius-md` | `--el-border-radius-base` | `var(--el-border-radius-base)` |

Store all tokens in a central `variables.scss` file.

### Rule 3: Prevent Overflow

After generating code, verify layout:

```
pencil_snapshot_layout({ nodeId: "frameId", problemsOnly: true })
```

For text content:
- Use `overflow: hidden; text-overflow: ellipsis; white-space: nowrap` on single-line text
- Use `show-overflow-tooltip` on `<el-table-column>` for table cells
- Set `min-width` on flex children to prevent shrink-to-zero

### Rule 4: Visual Verification After Each Section

After building each section (header, card grid, table, sidebar, etc.):

1. `pencil_get_screenshot({ nodeId: "targetFrame" })` — capture design screenshot
2. Compare with browser rendering (via browser tools or manual check)
3. `pencil_snapshot_layout({ problemsOnly: true })` — catch clipping/overlap
4. Fix discrepancies before proceeding

### Rule 5: Preserve Text, Icons, and Spacing Exactly

- Use the **exact same text labels** as the design
- Use the **exact same icons** (match icon names from design)
- Match **spacing values** precisely (gap, padding, margin)
- Match **font sizes and weights** exactly

### Rule 6: Reuse Existing Assets

Before generating any image or logo:
1. `pencil_batch_get({ patterns: [{ name: "logo|brand|icon" }] })` — search existing assets
2. If found, copy with `C()` (Copy operation) instead of regenerating
3. Only use `G()` (Generate) for genuinely new images

### Rule 7: Read Pencil Guidelines Before Code Generation

Before generating code from any `.pen` file:

```
pencil_get_guidelines({ topic: "code" })
```

This returns Pencil's built-in rules for translating design properties to code, including layout, color, and typography conventions specific to the current document.

### Rule 8: Section-by-Section Build

Never build an entire page at once. For each logical section:

1. **Plan** — identify which Element Plus components to use
2. **Build** — generate the Vue template + script + style
3. **Verify** — `pencil_get_screenshot` + browser comparison
4. **Fix** — address any discrepancies
5. **Proceed** — only move to next section after verification passes

## Workflow

### Step 0: Read Pencil Guidelines

```
pencil_get_guidelines({ topic: "code" })
pencil_get_guidelines({ topic: "design-system" })
```

### Step 1: Read Design Structure

```
pencil_get_editor_state({ include_schema: true })
pencil_batch_get({ nodeIds: ["targetFrameId"], readDepth: 5 })
```

### Step 2: Extract Design Tokens

```
pencil_get_variables()
```

Sync to `variables.scss`:

```scss
:root {
  --el-color-primary: #1a73e8;
  --el-color-success: #52c41a;
  --el-color-danger: #f5222d;
  --el-color-warning: #faad14;
  --bg-page: #F5F7FA;
  --bg-card: #ffffff;
  --bg-sidebar: #001529;
  --text-primary: #1A1A1A;
  --text-secondary: #4B5563;
  --text-muted: #9CA3AF;
}
```

### Step 3: Identify Components

```
pencil_batch_get({ patterns: [{ reusable: true }], readDepth: 3 })
```

Map each to Element Plus or create custom Vue SFC.

### Step 4: Generate Vue SFC

Standard template:

```vue
<template>
  <div class="page-name">
    <!-- Section content -->
  </div>
</template>

<script setup lang="ts">
import { ref, reactive, computed, onMounted } from 'vue'

// Props and types
// Reactive state
// Computed properties
// Methods
// Lifecycle hooks
</script>

<style scoped lang="scss">
.page-name {
  /* Use CSS custom properties from variables.scss */
}
</style>
```

### Step 5: Verify

1. `pencil_get_screenshot` — compare design vs implementation
2. Check all text labels match
3. Check all colors use CSS variables
4. Check responsive behavior

## Layout Mapping

| Pencil Property | Vue/CSS Implementation |
|----------------|----------------------|
| `layout: "vertical"` | `display: flex; flex-direction: column` |
| `layout: "horizontal"` | `display: flex; flex-direction: row` |
| `gap: N` | `gap: Npx` |
| `padding: N` | `padding: Npx` |
| `padding: [T, R, B, L]` | `padding: Tpx Rpx Bpx Lpx` |
| `width: "fill_container"` | `width: 100%` or `flex: 1` |
| `height: "fill_container"` | `height: 100%` or `flex: 1` |
| `width: "fit_content"` | `width: fit-content` |
| `alignItems: "center"` | `align-items: center` |
| `justifyContent: "space_between"` | `justify-content: space-between` |
| `cornerRadius` | `border-radius` (use CSS variable) |

## Typography Mapping

| Pencil fontSize | CSS |
|----------------|-----|
| 12 | `font-size: 12px` |
| 13 | `font-size: 13px` |
| 14 | `font-size: 14px` |
| 15 | `font-size: 15px` |
| 16 | `font-size: 16px` |
| 18 | `font-size: 18px` |
| 20 | `font-size: 20px` |
| 24 | `font-size: 24px` |

| Pencil fontWeight | CSS |
|------------------|-----|
| "400" | `font-weight: 400` (normal) |
| "500" | `font-weight: 500` (medium) |
| "600" | `font-weight: 600` (semibold) |
| "700" | `font-weight: 700` (bold) |

## Icon Mapping

| Pencil (Material Symbols) | Element Plus Icons |
|--------------------------|-------------------|
| `home` | `<HomeFilled />` |
| `settings` | `<Setting />` |
| `person` | `<User />` |
| `search` | `<Search />` |
| `add` | `<Plus />` |
| `close` | `<Close />` |
| `edit` | `<Edit />` |
| `delete` | `<Delete />` |
| `dashboard` | `<Odometer />` |
| `notifications` | `<Bell />` |
| `cloud` | `<Cloudy />` |
| `upload` | `<Upload />` |
| `download` | `<Download />` |
| `refresh` | `<Refresh />` |
| `arrow_forward` | `<ArrowRight />` |
| `arrow_back` | `<ArrowLeft />` |
| `expand_more` | `<ArrowDown />` |
| `list` | `<List />` |
| `folder` | `<Folder />` |
| `description` | `<Document />` |

## Common Vue Patterns

### Page with Search + Table + Pagination

See [references/page-patterns.md](references/page-patterns.md).

### Form Dialog

See [references/dialog-patterns.md](references/dialog-patterns.md).

### Dashboard Cards

See [references/dashboard-patterns.md](references/dashboard-patterns.md).

## Always Do

- Read `pencil_get_guidelines("code")` before generating code
- Read `pencil_get_variables()` and sync to `variables.scss`
- Use `pencil_batch_get({ reusable: true })` to find design system components
- Map to Element Plus first, only create custom components when needed
- Build and verify section by section, not all at once
- Copy existing logos/assets with `C()`, never regenerate
- Use scoped SCSS with CSS custom properties
- Define TypeScript interfaces for all data models
- Use `<script setup lang="ts">` syntax exclusively

## Never Do

- Hardcode hex colors — use CSS variables from Pencil tokens
- Skip `pencil_get_screenshot` verification after building a section
- Skip `pencil_snapshot_layout(problemsOnly: true)` overflow checks
- Create a monolithic single file for multi-component screens
- Use `any` type — define proper interfaces in `types/api.d.ts`
- Build entire page at once without section-by-section verification
- Regenerate logos/icons that already exist in the `.pen` file
- Ignore the component hierarchy from the Pencil design tree

## Common Mistakes

| Mistake | Correct Approach |
|---------|-----------------|
| Hardcoding `color: #1a73e8` | Use `color: var(--el-color-primary)` |
| Creating a custom button | Use `<el-button>` with props |
| Skipping `get_screenshot` | Always verify each section visually |
| Using `any` for types | Define TypeScript interfaces in `types/api.d.ts` |
| Ignoring design text labels | Copy exact text from Pencil design |
| Inline styles everywhere | Use scoped SCSS with CSS variables |
| Not checking responsive | Test with different viewport widths |
| Building whole page then checking | Build and verify section by section |
| Regenerating existing logos | Copy with `C()` from existing artboard |
| Skipping `get_guidelines("code")` | Always read guidelines before generating |

## Additional Resources

- [Page patterns](references/page-patterns.md) — Search + Table + Pagination template
- [Dialog patterns](references/dialog-patterns.md) — Form dialog template
- [Dashboard patterns](references/dashboard-patterns.md) — Dashboard card/chart template
- [Pencil Docs](https://docs.pencil.dev)
- [Design as Code](https://docs.pencil.dev/core-concepts/design-as-code)
- [Variables](https://docs.pencil.dev/core-concepts/variables)
- [Components](https://docs.pencil.dev/core-concepts/components)
- [Design to Code](https://docs.pencil.dev/design-and-code/design-to-code)
