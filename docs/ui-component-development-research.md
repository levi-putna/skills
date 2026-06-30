# UI Component Development Skills — Research Summary

> Research findings on best practices for building and testing UI elements and components (June 2026)

This document summarizes the research that informed the **component-library**, **component-development**, and **component-testing** skills.

## Executive Summary

In 2026, the industry standard for UI component development is **Component-Driven Development (CDD)** using **Storybook 8** as the primary development environment. Components are built in isolation, documented automatically, and tested at multiple layers before integration into the application.

**Key tooling:**
- **Storybook 8** — Component development, documentation, and interaction testing
- **Chromatic** — Visual regression testing (cloud) or Playwright (self-hosted)
- **@storybook/addon-a11y** — Accessibility auditing with axe-core
- **React Testing Library** — Unit testing with user-centric queries
- **Mock Service Worker (MSW)** — API mocking at the network boundary

## Component-Driven Development (CDD)

### Core principles

1. **Isolation first** — Build components independently before composing into pages
2. **Stories as specifications** — Each story is an executable example of component behaviour
3. **Test at multiple layers** — Interaction, visual, accessibility, unit tests
4. **Documentation from code** — JSDoc generates Storybook documentation automatically
5. **Design system thinking** — Components are reusable, composable building blocks

### Why CDD works

**Traditional page-first development problems:**
- Hard to reproduce specific component states (loading, error, edge cases)
- Components become coupled to page context
- Testing requires navigating through full application flows
- Design and development drift apart

**CDD advantages:**
- Any component state reachable in seconds
- Components are genuinely decoupled (you had to think about their API)
- Tests run in isolation without dependencies
- Design and code stay in sync through Storybook

## Storybook 8 (2026 standard)

### What's new in Storybook 8

- **Vite-first architecture** — 10x faster builds
- **Interaction testing** — `@storybook/test` with play functions for user simulation
- **Component Story Format 3 (CSF3)** — TypeScript-first, cleaner API
- **Auto-documentation** — Prop tables, descriptions from JSDoc
- **First-class React Server Components support**
- **Test runner** — Run interaction tests in CI

### Story structure (CSF3)

```typescript
import type { Meta, StoryObj } from '@storybook/react';
import { expect, userEvent, within } from '@storybook/test';

const meta = {
  title: 'UI/Button',
  component: Button,
  parameters: { layout: 'centered' },
  tags: ['autodocs'],
  argTypes: {
    variant: {
      control: 'select',
      options: ['default', 'destructive', 'outline'],
    },
  },
} satisfies Meta<typeof Button>;

export default meta;
type Story = StoryObj<typeof meta>;

export const Default: Story = {
  args: { children: 'Button' },
};

export const WithInteraction: Story = {
  args: { children: 'Click me' },
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);
    const button = canvas.getByRole('button');
    await userEvent.click(button);
    expect(button).toHaveFocus();
  },
};
```

### Storybook as test harness

Stories serve three purposes:
1. **Development environment** — Build components with hot reload
2. **Documentation** — Living examples with interactive controls
3. **Test fixtures** — Interaction tests run in stories, exportable to other test runners

## Testing Methodology

### Multi-layer testing strategy

Modern component testing uses complementary approaches:

| Layer | Tool | Purpose | When |
|-------|------|---------|------|
| **Interaction** | Storybook play functions | User behaviour simulation | Dev + CI |
| **Visual** | Chromatic / Playwright | Pixel-perfect appearance | CI (PR) |
| **Accessibility** | a11y addon + axe-core | WCAG compliance | Dev + CI |
| **Unit** | React Testing Library | Logic and edge cases | Dev + CI |

### Interaction testing (Storybook play functions)

**Philosophy:** Test how users interact with components, not internal implementation.

```typescript
export const FormValidation: Story = {
  play: async ({ canvasElement, step }) => {
    const canvas = within(canvasElement);
    
    await step('Submit empty form', async () => {
      const submit = canvas.getByRole('button', { name: /submit/i });
      await userEvent.click(submit);
    });
    
    await step('Verify validation errors', async () => {
      expect(await canvas.findByText(/email is required/i)).toBeInTheDocument();
    });
  },
};
```

**Best practices:**
- Use `step()` to organize test phases and create closure boundaries
- Query by role (`getByRole`) for accessibility and resilience
- Use `userEvent` (not `fireEvent`) for realistic event sequences
- Wait for async changes with `waitFor` and `findBy*` queries

### Visual regression testing

**Option 1: Chromatic (recommended for teams)**

Cloud-based visual testing integrated with Storybook:
- Automatic screenshot capture of all stories
- Cross-browser testing (Chrome, Firefox, Safari)
- PR comments with visual diff review UI
- Infrastructure-free setup

```bash
yarn chromatic --project-token=<token>
```

**Option 2: Playwright (self-hosted)**

For teams that need control over screenshot infrastructure:

```typescript
test('Button variants', async ({ page }) => {
  await page.goto('http://localhost:6006/?path=/story/ui-button--all-variants');
  const frame = page.frameLocator('#storybook-preview-iframe');
  await expect(frame.locator('[data-testid="story-root"]')).toHaveScreenshot();
});
```

### Accessibility testing

**Real-time (Storybook a11y addon):**
- Automatic audits in Storybook UI
- Reports WCAG violations with remediation guidance
- Runs on every story automatically

**CI (automated):**
- Test runner executes a11y checks on all stories
- Fails build on violations
- Integrates with GitHub Actions

**Checklist:**
- [ ] All interactive elements have accessible names
- [ ] Colour contrast meets WCAG AA (4.5:1)
- [ ] Keyboard navigation works (Tab, Enter, Escape)
- [ ] Form inputs have associated labels
- [ ] Focus indicators visible
- [ ] Screen reader compatibility

### Unit testing (React Testing Library)

**Query priority (RTL philosophy):**

1. `getByRole` — Best (matches how screen readers work)
2. `getByLabelText` — Good for forms
3. `getByPlaceholText` — OK for inputs
4. `getByText` — Good for content
5. `getByTestId` — Last resort only

**Query variants:**
- `getBy*` — Throws if not found (assert existence)
- `queryBy*` — Returns null (assert non-existence)
- `findBy*` — Returns promise (async elements)

**Example:**

```typescript
it('validates email format', async () => {
  const user = userEvent.setup();
  render(<LoginForm />);
  
  await user.type(screen.getByLabelText(/email/i), 'invalid-email');
  await user.click(screen.getByRole('button', { name: /sign in/i }));
  
  expect(await screen.findByText(/invalid email format/i)).toBeInTheDocument();
});
```

**Anti-patterns to avoid:**
- Testing internal component state or hooks
- Using `container.querySelector` instead of semantic queries
- Manual `act()` wrappers (RTL handles this)
- Testing that functions were called instead of user-visible outcomes

## shadcn/ui Composition Patterns

### Core philosophy: Composition over configuration

**Bad (prop-heavy):**
```typescript
<Card hasHeader title="Title" hasFooter footerContent={<Button />} />
```

**Good (composition):**
```typescript
<Card>
  <CardHeader>
    <CardTitle>Title</CardTitle>
  </CardHeader>
  <CardContent>{/* ... */}</CardContent>
  <CardFooter>
    <Button />
  </CardFooter>
</Card>
```

### Compound components with Context

For components with shared state between parts:

```typescript
const UserCardContext = React.createContext<{
  expanded: boolean;
  setExpanded: (expanded: boolean) => void;
} | null>(null);

export function UserCard({ children }: { children: React.ReactNode }) {
  const [expanded, setExpanded] = React.useState(false);
  return (
    <UserCardContext.Provider value={{ expanded, setExpanded }}>
      <Card>{children}</Card>
    </UserCardContext.Provider>
  );
}

export function UserCardContent({ children }: { children: React.ReactNode }) {
  const context = React.useContext(UserCardContext);
  if (!context?.expanded) return null;
  return <CardContent>{children}</CardContent>;
}
```

**Benefits:**
- No prop drilling
- Child components automatically "know" their parent
- Clean API for consumers
- Easy to extend

### Composition hierarchy rules

Always follow the correct nesting structure:

**Correct:**
```typescript
<Select>
  <SelectTrigger>
    <SelectValue />
  </SelectTrigger>
  <SelectContent>
    <SelectGroup>
      <SelectItem value="1">Option 1</SelectItem>
    </SelectGroup>
  </SelectContent>
</Select>
```

**Wrong:**
```typescript
<Select>
  <SelectContent>
    {/* Missing SelectGroup wrapper */}
    <SelectItem value="1">Option 1</SelectItem>
  </SelectContent>
</Select>
```

### Variant systems with CVA

Use class-variance-authority for systematic variant patterns:

```typescript
import { cva, type VariantProps } from 'class-variance-authority';

const buttonVariants = cva(
  'base-classes',
  {
    variants: {
      variant: {
        default: 'bg-primary text-primary-foreground',
        destructive: 'bg-destructive text-destructive-foreground',
      },
      size: {
        default: 'h-10 px-4',
        sm: 'h-9 px-3',
        lg: 'h-11 px-8',
      },
    },
    defaultVariants: {
      variant: 'default',
      size: 'default',
    },
  }
);
```

**Best practices:**
- Start with no variants; add only when patterns repeat
- Use semantic names (`variant="destructive"` not `variant="red"`)
- Use design system tokens (`bg-primary` not `bg-blue-500`)

## Vercel AI SDK UI Patterns

### Generative UI architecture

The LLM outputs structured data (Zod-validated JSON) that the client maps to a component registry:

```typescript
// Server defines tools with Zod schemas
const weatherTool = {
  name: 'displayWeather',
  parameters: z.object({
    location: z.string(),
    temperature: z.number(),
    conditions: z.string(),
  }),
};

// Client maintains registry: toolName → React component
const toolRegistry = {
  displayWeather: WeatherCard,
  displayStock: StockPrice,
};

// Render based on tool invocation parts
{messages.map(message => (
  message.parts.map(part => {
    if (part.type === 'tool-displayWeather') {
      const Component = toolRegistry['displayWeather'];
      return <Component {...part.output} />;
    }
  })
))}
```

### Tool invocation states

Each tool goes through three states:

1. **input-streaming** — Args still streaming (show skeleton)
2. **input-available** — Args complete, executing (show loading)
3. **output-available** — Done (show result)

Render appropriate UI for each state to avoid janky empty spaces.

## API Mocking with MSW

### Philosophy: Mock at the network boundary

**Why MSW over mocking fetch directly:**
- More real code is exercised (the entire request pipeline)
- Works identically in tests and Storybook
- Type-safe handlers when using typed API clients
- No component-level mocking (components remain unchanged)

### Handler factories pattern

```typescript
// types.ts
export type User = {
  id: number;
  name: string;
  role: string;
};

// handlers.ts
export function createUserHandlers(users: User[]) {
  return [
    http.get('/api/users', () => {
      return HttpResponse.json(users);
    }),
    http.post('/api/users', async ({ request }) => {
      const newUser = await request.json() as User;
      users.push(newUser);
      return HttpResponse.json(newUser, { status: 201 });
    }),
  ];
}
```

**Benefits:**
- Reusable across stories and tests
- Type-safe (breaks at compile time when API changes)
- Stateful mocks (CRUD operations work in memory)

### MSW in Storybook

```typescript
// .storybook/preview.ts
import { initialize, mswLoader } from 'msw-storybook-addon';

initialize();

export const loaders = [mswLoader];

// Individual story
export const WithData: Story = {
  parameters: {
    msw: {
      handlers: [
        http.get('/api/users', () => {
          return HttpResponse.json([/* mock data */]);
        }),
      ],
    },
  },
};
```

## Component Gallery Patterns

### Option 1: Storybook (recommended)

Industry standard component gallery with:
- Automatic component discovery
- Interactive prop controls
- Documentation generation
- Interaction testing
- Visual regression testing
- Accessibility auditing

**Access:** http://localhost:6006

### Option 2: Custom in-app gallery

For projects that need components browsable at a route (e.g., `/components`):

```typescript
// app/components/page.tsx
export default function ComponentsPage() {
  return <ComponentGallery />;
}

// components/gallery/component-registry.tsx
export const componentRegistry: ComponentRegistryEntry[] = [
  {
    id: 'button',
    name: 'Button',
    category: 'primitive',
    status: 'stable',
    preview: <Button>Click me</Button>,
    variants: [
      { name: 'Primary', preview: <Button>Primary</Button> },
      { name: 'Secondary', preview: <Button variant="secondary">Secondary</Button> },
    ],
  },
];
```

**Benefits:**
- Components browsable in the actual application
- Can integrate with app auth/theme
- Useful for client demos

**Drawbacks:**
- No automatic discovery (manual registry)
- No prop controls out of the box
- No built-in testing infrastructure

## Component Organization

### Folder structure

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
```

### Co-location principle

Keep related files together:
- Component implementation (`.tsx`)
- Story file (`.stories.tsx`)
- Test file (`.test.tsx`)

**Benefits:**
- Easy to find all files related to a component
- Encourages complete implementation (code + stories + tests)
- Makes components portable (can move entire folder)

## CI/CD Integration

### GitHub Actions workflow

```yaml
name: Component Tests

on:
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
      
      - run: yarn install
      - run: yarn build-storybook
      - run: yarn test-storybook        # Interaction tests
      - run: yarn test --run            # Unit tests
      
      - name: Visual regression
        uses: chromaui/action@latest
        with:
          projectToken: ${{ secrets.CHROMATIC_PROJECT_TOKEN }}
```

## Key Takeaways

1. **Storybook 8 is the industry standard** for component development in 2026
2. **Component-Driven Development** builds UI in isolation before page integration
3. **Test at multiple layers** — interaction, visual, accessibility, unit
4. **shadcn/ui patterns** — composition over configuration, compound components with Context
5. **React Testing Library** — query by role, use userEvent, test user behaviour not implementation
6. **MSW for mocking** — mock at network boundary, not component boundary
7. **Accessibility first** — WCAG compliance verified automatically in every story
8. **Stories are specifications** — executable examples that document and test simultaneously

## References

### Research sources

- Storybook Component Testing Guide (QASkills.sh, 2026)
- Storybook in 2026: Component-Driven Development (CODERCOPS)
- shadcn/ui Composition Patterns (BetterLink Blog, April 2026)
- The Ultimate shadcn/ui Handbook (shadcnspace.com, 2026)
- React Testing Library Patterns (Viprasol, 2026)
- Common mistakes with React Testing Library (Kent C. Dodds)
- Testing Design Systems at Scale (QASkills.sh, 2026)
- Vercel AI SDK UI Documentation (ai-sdk.dev)

### Further reading

- Component-Driven Development: 2025 Edition (DEV Community)
- Why Atomic Design and CDD Are the Perfect Match (DEV Community)
- Testing Design Systems: Component Libraries at Scale
- React Testing Library: Best Practices and Patterns
- How we turned Storybook into a behavioral verification engine (Red Hat)
