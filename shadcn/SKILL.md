---
name: shadcn
description: Manages shadcn components and projects — adding, searching, fixing, debugging, styling, and composing UI. Provides project context, component docs, and usage examples. Applies when working with shadcn/ui, component registries, presets, --preset codes, or any project with a components.json file.
user-invocable: false
allowed-tools: Bash(npx shadcn@latest *), Bash(pnpm dlx shadcn@latest *), Bash(bunx --bun shadcn@latest *)
---

## Rules

### Styling & Tailwind

- **Use semantic colors.** `bg-primary`, `text-muted-foreground` — never raw values like `bg-blue-500`.

- **Use built-in variants before custom styles.** `variant="outline"`, `size="sm"`, etc. Only add custom styles when variants don't achieve the desired result, by asking the user.

- **Use `className` for layout, not styling.** Never override component colors or typography until user requests. To customize prefer this in order: 1) built-in variants, 2) semantic color tokens, 3) css variables.

- **No `space-x-*` or `space-y-*`.** Use `flex` with `gap-*`. For vertical stacks, `flex flex-col gap-*`.

- **Use `size-*` when width and height are equal.** `size-10` not `w-10 h-10` — applies to icons, avatars, skeletons, etc.

- **Use `truncate` shorthand.** Not `overflow-hidden text-ellipsis whitespace-nowrap`.

- **No manual `dark:` color overrides.** Use semantic tokens (`bg-background`, `text-muted-foreground`).

- **Use `cn()` for conditional classes.** Don't write manual template literal ternaries.

- **No manual `z-index` on overlay components.** Dialog, Sheet, Popover, etc. handle their own stacking.

### Forms & Inputs

- **Forms use `FieldGroup` + `Field`.** — never raw `div` with `space-y-*` or `grid gap-*` for form layout. Use `Field orientation="horizontal"` for settings pages. Use `FieldLabel className="sr-only"` for visually hidden labels. Choosing form controls: 1) Simple text input → `Input`, 2) Dropdown with predefined options → `Select`, 3) Searchable dropdown → `Combobox`, 4) Native HTML select (no JS) → `native-select`, 5) Boolean toggle → `Switch` (for settings) or `Checkbox` (for forms), 6) Single choice from few options → `RadioGroup`, 7) Toggle between 2–5 options → `ToggleGroup` + `ToggleGroupItem`, 8) OTP/verification code → `InputOTP`, 9) Multi-line text → `Textarea`.

- **`InputGroup` uses `InputGroupInput`/`InputGroupTextarea`.** Never raw `Input`/`Textarea` inside `InputGroup`.

- **Buttons inside inputs use `InputGroup` + `InputGroupAddon`.**

- **Option sets (2–7 choices) use `ToggleGroup`.** Don't loop `Button` with manual active state.

- **`FieldSet` + `FieldLegend` for grouping related checkboxes/radios.** Don't use a `div` with a heading.

- **Field validation uses `data-invalid` + `aria-invalid`.** For invalid: `data-invalid` on `Field`, `aria-invalid` on the control. For disabled: `data-disabled` on `Field`, `disabled` on the control. Works for all controls.

### Component Composition & Structure

- **Items always inside their Group.** `SelectItem` → `SelectGroup`. `DropdownMenuItem` → `DropdownMenuGroup`. `CommandItem` → `CommandGroup`. This applies to all group-based components.

- **Choose between overlays**: 1) Focused task that requires input → `Dialog`, 2) Destructive action confirmation → `AlertDialog`, 3) Side panel with details or filters → `Sheet`, 4) Mobile-first bottom panel → `Drawer`, 5) Quick preview of content on hover → `HoverCard`, 6) Small contextual content on click → `Popover`.

- **Dialog, Sheet, and Drawer always need a Title.** `DialogTitle`, `SheetTitle`, `DrawerTitle` required for accessibility. Use `className="sr-only"` if visually hidden.

- **Use full Card composition.** `CardHeader`/`CardTitle`/`CardDescription`/`CardContent`/`CardFooter`. Don't dump everything in `CardContent`.

- **Button has no `isPending`/`isLoading`.** Compose with `Spinner` + `data-icon` + `disabled`.

- **`TabsTrigger` must be inside `TabsList`.** Never render triggers directly in `Tabs`. Use `asChild` (radix) or `render` (base) for custom triggers.

- **`Avatar` always needs `AvatarFallback`.** For when the image fails to load.

### Use Components, Not Custom Markup

- **Use existing components before custom markup.** Check if a component exists before writing a styled `div`.

- **Callouts use `Alert`.** Don't build custom styled divs.

- **Empty states use `Empty`.** Don't build custom empty state markup.

- **Toast via `sonner`.** Use `toast()` from `sonner`.

- **Use `Separator`** instead of `<hr>` or `<div className="border-t">`.

- **Use `Skeleton`** for loading placeholders. No custom `animate-pulse` divs.

- **Use `Badge`** instead of custom styled spans.

### Icons

- **Always use the project's configured `iconLibrary` for imports.** Check the `iconLibrary` field from project context: `lucide` → `lucide-react`, `tabler` → `@tabler/icons-react`, etc. Never assume `lucide-react`.

- **Icons in `Button` use `data-icon`.** `data-icon="inline-start"` or `data-icon="inline-end"` on the icon.

- **No sizing classes on icons inside components.** Components handle icon sizing via CSS. No `size-4` or `w-4 h-4`.

- **Pass icons as objects, not string keys.** `icon={CheckIcon}`, not a string lookup.

### CLI

- **Never decode or fetch preset codes manually.** Pass them directly to `npx shadcn@latest init --preset <code>`.

## Key Patterns

The most common patterns that differentiate correct shadcn/ui code. For edge cases, see rules above.

```tsx
// Form layout: FieldGroup + Field, not div + Label.
<FieldGroup>
  <Field>
    <FieldLabel htmlFor="email">Email</FieldLabel>
    <Input id="email" />
  </Field>
</FieldGroup>

// Validation: data-invalid on Field, aria-invalid on the control.
<Field data-invalid>
  <FieldLabel>Email</FieldLabel>
  <Input aria-invalid />
  <FieldDescription>Invalid email.</FieldDescription>
</Field>

// Icons in buttons: data-icon, no sizing classes.
<Button>
  <SearchIcon data-icon="inline-start" />
  Search
</Button>

// Spacing: gap-*, not space-y-*.
<div className="flex flex-col gap-4"> // correct
<div className="space-y-4"> // wrong

// Equal dimensions: size-*, not w-* h-*.
<Avatar className="size-10"> // correct
<Avatar className="w-10 h-10"> // wrong

// Status colors: Badge variants or semantic tokens, not raw colors.
<Badge variant="secondary">+20.1%</Badge> // correct
<span className="text-emerald-600">+20.1%</span> // wrong
```

## Component Selection

| Need | Use |
| --- | --- |
| Button/action | `Button` with appropriate variant |
| Form inputs | `Input`, `Select`, `Combobox`, `Switch`, `Checkbox`, `RadioGroup`, `Textarea`, `InputOTP`, `Slider` |
| Toggle between 2–5 options | `ToggleGroup` + `ToggleGroupItem` |
| Data display | `Table`, `Card`, `Badge`, `Avatar` |
| Navigation | `Sidebar`, `NavigationMenu`, `Breadcrumb`, `Tabs`, `Pagination` |
| Overlays | `Dialog` (modal), `Sheet` (side panel), `Drawer` (bottom sheet), `AlertDialog` (confirmation) |
| Feedback | `sonner` (toast), `Alert`, `Progress`, `Skeleton`, `Spinner` |
| Command palette | `Command` inside `Dialog` |
| Charts | `Chart` (wraps Recharts) |
| Layout | `Card`, `Separator`, `Resizable`, `ScrollArea`, `Accordion`, `Collapsible` |
| Empty states | `Empty` |
| Menus | `DropdownMenu`, `ContextMenu`, `Menubar` |
| Tooltips/info | `Tooltip`, `HoverCard`, `Popover` |

## Key Fields

The injected project context contains these key fields:

- **`aliases`** → use the actual alias prefix for imports (e.g. `@/`, `~/`), never hardcode.

- **`isRSC`** → when `true`, components using `useState`, `useEffect`, event handlers, or browser APIs need `"use client"` at the top of the file. Always reference this field when advising on the directive.

- **`tailwindVersion`** → `"v4"` uses `@theme inline` blocks; `"v3"` uses `tailwind.config.js`.

- **`tailwindCssFile`** → the global CSS file where custom CSS variables are defined. Always edit this file, never create a new one.

- **`style`** → component visual treatment (e.g. `nova`, `vega`).

- **`iconLibrary`** → determines icon imports. Use `lucide-react` for `lucide`, `@tabler/icons-react` for `tabler`, etc. Never assume `lucide-react`.

- **`framework`** → routing and file conventions (e.g. Next.js App Router vs Vite SPA).


----








----









----

## Current Project Context

The JSON contains the project config and installed components.

```json
!`npx shadcn@latest info --json`
```

## Principles

1. **Use existing components first.** Use `npx shadcn@latest search` to check registries before writing custom UI.
2. **Compose, don't reinvent.** Settings page = Tabs + Card + form controls. Dashboard = Sidebar + Card + Chart + Table.
3. **Use built-in variants before custom styles.** `variant="outline"`, `size="sm"`, etc.
4. **Use semantic colors.** `bg-primary`, `text-muted-foreground` — never raw values like `bg-blue-500`.

## Critical Rules

These rules are **always enforced**. Each links to a file with Incorrect/Correct code pairs.

