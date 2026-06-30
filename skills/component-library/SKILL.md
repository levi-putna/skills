---
name: component-library
description: >-
  Set up and manage a component library showcase that displays all UI elements in isolation.
  Creates a living gallery for browsing, testing, and documenting components with live previews.
  Use when starting a new project, adding component showcase infrastructure, or when the user
  needs a component gallery, playground, or visual component browser.
---

# Component Library

Set up a living component library showcase that displays all UI elements in isolation, making them browsable, testable, and reusable. This is the foundation for component-driven development (CDD).

Announce at start: "I'm using the component-library skill to set up your component showcase infrastructure."

## What this skill creates

A component library showcase provides:

1. **Visual gallery** — Browse all components with live previews and variants
2. **Interactive playground** — Test components with different props and states
3. **Component documentation** — Auto-generated docs from code
4. **Isolation environment** — Develop and test components outside the main app
5. **Design system foundation** — Central registry of reusable UI elements

## Tech stack decision

The industry standard in 2026 is **Storybook 8** with Next.js integration. It provides:

- Component development in isolation
- Interaction testing with play functions
- Visual regression testing with Chromatic
- Accessibility testing with a11y addon
- Auto-generated documentation
- Mock Service Worker (MSW) integration for API mocking

Alternative: For projects requiring a custom in-app gallery (like a `/components` route), this skill will scaffold a Next.js route-based gallery instead.

**Ask the user:** "Would you like to use Storybook (recommended, industry standard) or build a custom in-app component gallery route?"

## Prerequisites

Before starting:

1. Check if `package.json` exists — this skill assumes a Next.js/React project
2. Read `docs/technical/tech-stack.md` if it exists — respect locked (🔒) decisions
3. Read `docs/design/design-system.md` if it exists — component previews must use the design tokens and standards from it
4. If the project has UI but no `docs/design/design-system.md`, run **design-system** before setting up component work
5. Check if Storybook is already installed (`@storybook/react` in dependencies)
6. Identify existing component directories (typically `components/`, `src/components/`, `app/components/`)

## Installation (Storybook path)

### Step 1: Install Storybook

```bash
npx storybook@latest init --builder vite
```

This auto-detects the project framework and installs:
- `@storybook/react` — Core Storybook for React
- `@storybook/addon-essentials` — Essential addons (docs, controls, actions, viewport)
- `@storybook/addon-interactions` — Interaction testing
- `@storybook/addon-a11y` — Accessibility auditing
- `@storybook/test-runner` — CI test execution

### Step 2: Configure Next.js compatibility

Create or update `.storybook/main.ts`:

```typescript
import type { StorybookConfig } from '@storybook/react-vite';
import path from 'path';

const config: StorybookConfig = {
  stories: [
    '../stories/**/*.mdx',
    '../stories/**/*.stories.@(js|jsx|ts|tsx)',
    '../components/**/*.stories.@(js|jsx|ts|tsx)',
    '../app/components/**/*.stories.@(js|jsx|ts|tsx)',
  ],
  addons: [
    '@storybook/addon-essentials',
    '@storybook/addon-interactions',
    '@storybook/addon-a11y',
  ],
  framework: {
    name: '@storybook/react-vite',
    options: {},
  },
  docs: {
    autodocs: 'tag',
  },
  viteFinal: async (config) => {
    // Handle Next.js path aliases
    if (config.resolve) {
      config.resolve.alias = {
        ...config.resolve.alias,
        '@': path.resolve(__dirname, '../'),
      };
    }
    return config;
  },
};

export default config;
```

### Step 3: Add preview configuration

Create `.storybook/preview.ts`:

```typescript
import type { Preview } from '@storybook/react';
import '../app/globals.css'; // Import your global styles

const preview: Preview = {
  parameters: {
    controls: {
      matchers: {
        color: /(background|color)$/i,
        date: /Date$/,
      },
    },
    backgrounds: {
      default: 'light',
      values: [
        { name: 'light', value: '#ffffff' },
        { name: 'dark', value: '#1a1a1a' },
      ],
    },
  },
  tags: ['autodocs'],
};

export default preview;
```

### Step 4: Add NPM scripts

Update `package.json`:

```json
{
  "scripts": {
    "storybook": "storybook dev -p 6006",
    "build-storybook": "storybook build",
    "test-storybook": "test-storybook"
  }
}
```

### Step 5: Create example stories structure

Create `components/ui/.storybook-helpers.tsx` for shared decorators:

```typescript
import React from 'react';
import type { Decorator } from '@storybook/react';

/**
 * Decorator for components that need theme context
 */
export const withTheme: Decorator = (Story) => {
  return (
    <div className="font-sans antialiased">
      <Story />
    </div>
  );
};

/**
 * Decorator for components that need padding
 */
export const withPadding: Decorator = (Story) => {
  return (
    <div className="p-8">
      <Story />
    </div>
  );
};
```

### Step 6: Document the component library structure

Create `docs/component-library.md`:

```markdown
# Component Library

Our component library is built on Storybook 8 with the following structure:

## Running Storybook

```bash
# Development
yarn storybook

# Build for production
yarn build-storybook

# Run interaction tests
yarn test-storybook
```

## Component categories

### Primitives (`components/ui/`)
Base components from shadcn/ui. Co-locate `.stories.tsx` files with components.

### Composed (`components/composed/`)
Application-specific compositions of primitives. Co-locate stories.

### Patterns (`components/patterns/`)
Complex reusable patterns (forms, dashboards, layouts). Co-locate stories.

### AI Elements (`components/ai/`)
Vercel AI SDK UI components. Co-locate stories.

## Story naming convention

```
ComponentName.stories.tsx
```

Each story file exports:
- `default` — Meta configuration
- Named exports — Individual stories (variants, states, interactions)

## Writing stories

See `component-development` skill for detailed story-writing guidance.

## Testing strategy

1. **Visual testing** — Storybook renders all variants
2. **Interaction testing** — Play functions simulate user behaviour
3. **Accessibility testing** — a11y addon audits WCAG compliance
4. **Unit testing** — React Testing Library for logic (separate files)

## Integration with main app

Components are developed in Storybook first, then imported into the app:

```tsx
import { Button } from '@/components/ui/button';
```

All stories must:
- Show all meaningful prop variants
- Include interaction tests for stateful behaviour
- Pass a11y audits
- Document props with JSDoc
- Demonstrate design-system conformance (tokens, typography, spacing, states)
```

## Installation (Custom gallery path)

If the user chooses a custom in-app gallery:

### Step 1: Create gallery route

Create `app/components/page.tsx`:

```typescript
import { ComponentGallery } from '@/components/gallery/component-gallery';
import { Metadata } from 'next';

export const metadata: Metadata = {
  title: 'Component Library',
  description: 'Browse and test UI components',
};

export default function ComponentsPage() {
  return <ComponentGallery />;
}
```

### Step 2: Create gallery component

Create `components/gallery/component-gallery.tsx`:

```typescript
'use client';

import { useState } from 'react';
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from '@/components/ui/card';
import { Tabs, TabsContent, TabsList, TabsTrigger } from '@/components/ui/tabs';
import { Badge } from '@/components/ui/badge';
import { componentRegistry } from './component-registry';

/**
 * Component library gallery for browsing and testing UI components in isolation
 */
export function ComponentGallery() {
  const [selectedCategory, setSelectedCategory] = useState('all');
  
  const categories = ['all', ...new Set(componentRegistry.map(c => c.category))];
  
  const filteredComponents = selectedCategory === 'all'
    ? componentRegistry
    : componentRegistry.filter(c => c.category === selectedCategory);

  return (
    <div className="container mx-auto py-8">
      <div className="mb-8">
        <h1 className="text-4xl font-bold mb-2">Component Library</h1>
        <p className="text-muted-foreground">
          Browse and test all UI components in isolation
        </p>
      </div>

      <Tabs value={selectedCategory} onValueChange={setSelectedCategory}>
        <TabsList>
          {categories.map(cat => (
            <TabsTrigger key={cat} value={cat}>
              {cat.charAt(0).toUpperCase() + cat.slice(1)}
            </TabsTrigger>
          ))}
        </TabsList>

        <TabsContent value={selectedCategory} className="mt-6">
          <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
            {filteredComponents.map(component => (
              <Card key={component.id}>
                <CardHeader>
                  <div className="flex items-start justify-between">
                    <CardTitle>{component.name}</CardTitle>
                    <Badge variant="outline">{component.status}</Badge>
                  </div>
                  <CardDescription>{component.description}</CardDescription>
                </CardHeader>
                <CardContent>
                  <div className="border rounded-lg p-4 bg-background">
                    {component.preview}
                  </div>
                </CardContent>
              </Card>
            ))}
          </div>
        </TabsContent>
      </Tabs>
    </div>
  );
}
```

### Step 3: Create component registry

Create `components/gallery/component-registry.tsx`:

```typescript
import { ReactNode } from 'react';

export interface ComponentRegistryEntry {
  id: string;
  name: string;
  description: string;
  category: 'primitive' | 'composed' | 'pattern' | 'ai';
  status: 'stable' | 'beta' | 'experimental';
  preview: ReactNode;
  variants?: Array<{
    name: string;
    preview: ReactNode;
  }>;
}

/**
 * Registry of all components in the library.
 * 
 * To add a component:
 * 1. Import it
 * 2. Add an entry with id, name, description, category, status
 * 3. Provide a preview (default variant)
 * 4. Optionally add variants array for additional states
 */
export const componentRegistry: ComponentRegistryEntry[] = [
  // Add components here as they're created
  // Example:
  // {
  //   id: 'button',
  //   name: 'Button',
  //   description: 'Clickable button component with variants',
  //   category: 'primitive',
  //   status: 'stable',
  //   preview: <Button>Click me</Button>,
  //   variants: [
  //     { name: 'Primary', preview: <Button>Primary</Button> },
  //     { name: 'Secondary', preview: <Button variant="secondary">Secondary</Button> },
  //   ],
  // },
];
```

### Step 4: Document the custom gallery

Create `docs/component-library.md`:

```markdown
# Component Library

Our component library is accessible at `/components` when running the development server.

## Adding components to the gallery

1. Create your component following the `component-development` skill
2. Import it in `components/gallery/component-registry.tsx`
3. Add a registry entry with preview and metadata
4. The gallery will automatically display it

## Component categories

- **primitive** — Base UI elements (buttons, inputs, cards)
- **composed** — Combinations of primitives
- **pattern** — Complex reusable patterns (forms, dashboards)
- **ai** — AI-powered components (Vercel AI SDK)

## Status levels

- **stable** — Production-ready, tested, documented
- **beta** — Functional but API may change
- **experimental** — Under development

## Testing components

Each component should have its own test file using React Testing Library.
See `component-testing` skill for guidance.
```

## shadcn/ui integration

If the project uses shadcn/ui (check for `components.json`):

1. **Respect existing components** — Do not modify files in `components/ui/` directly
2. **Create composed components** — Build new components that use shadcn primitives
3. **Follow composition patterns** — Use compound components, avoid prop drilling
4. **Use semantic tokens** — Use `bg-primary`, `text-muted-foreground`, not raw colours

Read the composition rules from the shadcn skill if available.

## Design-system integration

The component library is the visible enforcement point for the design system:

- Global Storybook preview must load the same CSS variables/theme tokens as the app
- Stories should include representative light/dark or theme states when the design system supports them
- Component categories should match the design-system taxonomy
- Any new component story should include a note or arg that shows which design-system variants/tokens it uses
- Visual regression snapshots should protect brand colours, typography, spacing, and component states from drift

## Vercel AI SDK components

If the project uses Vercel AI SDK (`ai` package in dependencies):

Create an AI components category (`components/ai/`) with stories showing:

- Tool invocation rendering
- Streaming states
- Multi-step flows
- Error states

## Component categories structure

Organise components by complexity:

```
components/
  ui/              # Primitives (shadcn/ui or base components)
    button/
      button.tsx
      button.stories.tsx
      button.test.tsx
  
  composed/        # Application-specific combinations
    user-card/
      user-card.tsx
      user-card.stories.tsx
      user-card.test.tsx
  
  patterns/        # Complex reusable patterns
    dashboard-header/
      dashboard-header.tsx
      dashboard-header.stories.tsx
      dashboard-header.test.tsx
  
  ai/              # AI SDK components
    message-bubble/
      message-bubble.tsx
      message-bubble.stories.tsx
      message-bubble.test.tsx
  
  gallery/         # Gallery infrastructure (custom path only)
```

## Hand off

After installation:

> "Component library infrastructure is ready. Run `yarn storybook` to open the gallery at http://localhost:6006.
>
> Next steps:
> - Use **component-development** skill to create new components with stories
> - Use **component-testing** skill to add interaction and visual regression tests
> - See `docs/component-library.md` for usage and conventions."

If the user wants to start creating components immediately, offer to invoke the **component-development** skill.

## Principles

- **Isolation first** — Components are developed and tested independently before integration
- **Visual documentation** — Every component has live preview stories
- **Accessibility by default** — All components pass a11y audits
- **Testability** — Stories serve as both documentation and test fixtures
- **Design system thinking** — Components are reusable, composable building blocks
