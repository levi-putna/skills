---
name: component-development
description: >-
  Build UI components in isolation following component-driven development best practices.
  Creates components with stories, variants, interaction tests, and documentation.
  Use when creating new UI components, adding component variants, or when the user needs
  to build reusable, testable, documented UI elements.
---

# Component Development

Build UI components in isolation following component-driven development (CDD) best practices. Components are developed with stories first, then integrated into the application.

Announce at start: "I'm using the component-development skill to build this component."

## Component-Driven Development (CDD)

CDD is building UI components independently before assembling them into pages:

1. **Design the component API** — Props, variants, states
2. **Create stories** — Visual examples of all states
3. **Build the component** — Implementation with TypeScript
4. **Add interactions** — User event simulation and testing
5. **Document** — JSDoc, prop tables, usage examples
6. **Integrate** — Use in the application

## Prerequisites

Before building a component:

1. **Component library exists** — Use **component-library** skill if not set up
2. **Check for existing components** — Search for similar components before creating new ones
3. **Check design system** — If shadcn/ui is installed, prefer composing existing primitives
4. **Read requirements** — If `docs/technical/` exists, check for component specifications

## Component creation workflow

### Step 1: Plan the component API

Before writing code, define:

```typescript
// Component interface (planning stage)
interface ButtonProps {
  variant?: 'default' | 'destructive' | 'outline' | 'secondary' | 'ghost' | 'link';
  size?: 'default' | 'sm' | 'lg' | 'icon';
  disabled?: boolean;
  loading?: boolean;
  children: React.ReactNode;
  onClick?: () => void;
}
```

**API design principles:**

- **Minimal by default** — Start with no variants; add only when patterns repeat
- **Composition over configuration** — Prefer children over props for layout control
- **Semantic naming** — Use `variant="destructive"` not `variant="red"`
- **Controlled state** — For forms, always support both controlled and uncontrolled modes
- **Accessibility first** — Proper ARIA attributes, keyboard navigation, focus management

### Step 2: Create the story file first

Following TDD principles, create the story file **before** the component implementation.

**File location:**
```
components/
  ui/
    button/
      button.stories.tsx  ← Create this first
      button.tsx          ← Then this
      button.test.tsx     ← Then this
```

**Story template (CSF3 format):**

```typescript
import type { Meta, StoryObj } from '@storybook/react';
import { expect, userEvent, within } from '@storybook/test';
import { Button } from './button';

/**
 * Button component displays a clickable button with multiple variants and sizes.
 * 
 * ## Usage
 * 
 * ```tsx
 * <Button variant="default">Click me</Button>
 * ```
 */
const meta = {
  title: 'UI/Button',
  component: Button,
  parameters: {
    layout: 'centered',
  },
  tags: ['autodocs'],
  argTypes: {
    variant: {
      control: 'select',
      options: ['default', 'destructive', 'outline', 'secondary', 'ghost', 'link'],
      description: 'Visual style variant',
    },
    size: {
      control: 'select',
      options: ['default', 'sm', 'lg', 'icon'],
      description: 'Size variant',
    },
    disabled: {
      control: 'boolean',
      description: 'Disabled state',
    },
  },
} satisfies Meta<typeof Button>;

export default meta;
type Story = StoryObj<typeof meta>;

/**
 * Default button with primary styling
 */
export const Default: Story = {
  args: {
    children: 'Button',
    variant: 'default',
  },
};

/**
 * Destructive action button for dangerous operations
 */
export const Destructive: Story = {
  args: {
    children: 'Delete',
    variant: 'destructive',
  },
};

/**
 * Outline button for secondary actions
 */
export const Outline: Story = {
  args: {
    children: 'Cancel',
    variant: 'outline',
  },
};

/**
 * Small button size
 */
export const Small: Story = {
  args: {
    children: 'Small',
    size: 'sm',
  },
};

/**
 * Large button size
 */
export const Large: Story = {
  args: {
    children: 'Large',
    size: 'lg',
  },
};

/**
 * Disabled state
 */
export const Disabled: Story = {
  args: {
    children: 'Disabled',
    disabled: true,
  },
};

/**
 * Loading state with spinner
 */
export const Loading: Story = {
  args: {
    children: 'Loading...',
    loading: true,
  },
};

/**
 * Interactive test: Click handling
 */
export const ClickTracking: Story = {
  args: {
    children: 'Click me',
  },
  play: async ({ args, canvasElement, step }) => {
    const canvas = within(canvasElement);
    
    await step('User clicks button', async () => {
      const button = canvas.getByRole('button', { name: /click me/i });
      await userEvent.click(button);
      
      // In real scenario, this would verify onClick was called
      // or check for side effects in the DOM
    });
  },
};

/**
 * Interactive test: Keyboard navigation
 */
export const KeyboardNavigation: Story = {
  args: {
    children: 'Press Enter',
  },
  play: async ({ canvasElement, step }) => {
    const canvas = within(canvasElement);
    
    await step('User navigates with Tab', async () => {
      const button = canvas.getByRole('button');
      button.focus();
      expect(button).toHaveFocus();
    });
    
    await step('User presses Enter', async () => {
      const button = canvas.getByRole('button');
      await userEvent.keyboard('{Enter}');
      // Verify action occurred
    });
  },
};
```

**Story structure:**

1. **Meta** — Component metadata, title, parameters, argTypes
2. **Default export** — Meta configuration
3. **Story exports** — Individual variants and states
4. **JSDoc comments** — Describe each story's purpose
5. **Interaction tests** — Play functions for complex behaviour

### Step 3: Implement the component

Now create the component to satisfy the stories.

**Component template:**

```typescript
import * as React from 'react';
import { cva, type VariantProps } from 'class-variance-authority';
import { cn } from '@/lib/utils';

/**
 * Button variant styles using CVA
 */
const buttonVariants = cva(
  // Base styles
  'inline-flex items-center justify-center gap-2 whitespace-nowrap rounded-md text-sm font-medium transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:pointer-events-none disabled:opacity-50',
  {
    variants: {
      variant: {
        default: 'bg-primary text-primary-foreground hover:bg-primary/90',
        destructive: 'bg-destructive text-destructive-foreground hover:bg-destructive/90',
        outline: 'border border-input bg-background hover:bg-accent hover:text-accent-foreground',
        secondary: 'bg-secondary text-secondary-foreground hover:bg-secondary/80',
        ghost: 'hover:bg-accent hover:text-accent-foreground',
        link: 'text-primary underline-offset-4 hover:underline',
      },
      size: {
        default: 'h-10 px-4 py-2',
        sm: 'h-9 rounded-md px-3',
        lg: 'h-11 rounded-md px-8',
        icon: 'h-10 w-10',
      },
    },
    defaultVariants: {
      variant: 'default',
      size: 'default',
    },
  }
);

/**
 * Button component props interface
 */
export interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {
  /**
   * Loading state - shows spinner and disables interaction
   */
  loading?: boolean;
}

/**
 * Button component
 * 
 * A versatile button component with multiple variants, sizes, and states.
 * 
 * @example
 * ```tsx
 * <Button variant="default">Click me</Button>
 * <Button variant="destructive" size="sm">Delete</Button>
 * <Button variant="outline" disabled>Disabled</Button>
 * ```
 */
const Button = React.forwardRef<HTMLButtonElement, ButtonProps>(
  ({ className, variant, size, loading, disabled, children, ...props }, ref) => {
    return (
      <button
        className={cn(buttonVariants({ variant, size, className }))}
        ref={ref}
        disabled={disabled || loading}
        aria-busy={loading}
        {...props}
      >
        {loading && (
          <span className="animate-spin" aria-hidden="true">
            ⏳
          </span>
        )}
        {children}
      </button>
    );
  }
);

Button.displayName = 'Button';

export { Button, buttonVariants };
```

**Component implementation principles:**

- **TypeScript** — Full type safety with proper interfaces
- **Accessibility** — ARIA attributes, semantic HTML, keyboard support
- **Variants with CVA** — class-variance-authority for systematic style variants
- **Semantic tokens** — Use design system tokens (`bg-primary`, not `bg-blue-500`)
- **forwardRef** — Allow parent components to access the DOM node
- **Display name** — Set for better DevTools debugging

### Step 4: Verify in Storybook

Run Storybook and verify:

```bash
yarn storybook
```

**Checklist:**

- [ ] All variants render correctly
- [ ] Props controls work in the Controls panel
- [ ] Interaction tests pass (play functions execute)
- [ ] a11y addon shows no violations
- [ ] Documentation auto-generates correctly

### Step 5: Add unit tests (optional but recommended)

Create `button.test.tsx` using React Testing Library:

```typescript
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { Button } from './button';

describe('Button', () => {
  it('renders with default props', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByRole('button', { name: /click me/i })).toBeInTheDocument();
  });

  it('handles click events', async () => {
    const user = userEvent.setup();
    const handleClick = jest.fn();
    
    render(<Button onClick={handleClick}>Click me</Button>);
    
    const button = screen.getByRole('button', { name: /click me/i });
    await user.click(button);
    
    expect(handleClick).toHaveBeenCalledTimes(1);
  });

  it('is disabled when disabled prop is true', () => {
    render(<Button disabled>Click me</Button>);
    expect(screen.getByRole('button')).toBeDisabled();
  });

  it('shows loading state', () => {
    render(<Button loading>Click me</Button>);
    const button = screen.getByRole('button');
    expect(button).toHaveAttribute('aria-busy', 'true');
    expect(button).toBeDisabled();
  });
});
```

**Testing principles:**

- **User-centric queries** — Use `getByRole`, `getByLabelText`, not `getByTestId`
- **Realistic interactions** — Use `userEvent` not `fireEvent`
- **Test behaviour, not implementation** — Verify what users see, not internal state
- **Accessibility assertions** — Check ARIA attributes, disabled states

## shadcn/ui composition patterns

When building components that use shadcn/ui primitives:

### Use compound components with Context

```typescript
import * as React from 'react';
import { Card, CardHeader, CardTitle, CardContent } from '@/components/ui/card';

/**
 * Context for sharing state between UserCard components
 */
const UserCardContext = React.createContext<{
  expanded: boolean;
  setExpanded: (expanded: boolean) => void;
} | null>(null);

/**
 * Main UserCard component with shared state
 */
export function UserCard({ children }: { children: React.ReactNode }) {
  const [expanded, setExpanded] = React.useState(false);

  return (
    <UserCardContext.Provider value={{ expanded, setExpanded }}>
      <Card>{children}</Card>
    </UserCardContext.Provider>
  );
}

/**
 * UserCard header subcomponent
 */
export function UserCardHeader({ name, role }: { name: string; role: string }) {
  const context = React.useContext(UserCardContext);
  
  return (
    <CardHeader>
      <CardTitle>{name}</CardTitle>
      <p className="text-sm text-muted-foreground">{role}</p>
    </CardHeader>
  );
}

/**
 * UserCard content subcomponent (conditionally rendered based on expanded state)
 */
export function UserCardContent({ children }: { children: React.ReactNode }) {
  const context = React.useContext(UserCardContext);
  
  if (!context?.expanded) return null;
  
  return <CardContent>{children}</CardContent>;
}
```

### Follow composition hierarchy

Always nest components correctly:

```typescript
// ✅ Correct
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

// ❌ Wrong - SelectItem must be inside SelectGroup
<Select>
  <SelectTrigger>
    <SelectValue />
  </SelectTrigger>
  <SelectContent>
    <SelectItem value="1">Option 1</SelectItem>
  </SelectContent>
</Select>
```

## Vercel AI SDK patterns

For AI-powered components using Vercel AI SDK:

```typescript
import { useChat } from '@ai-sdk/react';

/**
 * Story for AI chat component with streaming
 */
export const StreamingChat: Story = {
  decorators: [
    (Story) => {
      // Mock the AI response
      return (
        <div>
          <Story />
        </div>
      );
    },
  ],
  play: async ({ canvasElement, step }) => {
    const canvas = within(canvasElement);
    
    await step('User types message', async () => {
      const input = canvas.getByRole('textbox');
      await userEvent.type(input, 'Hello AI');
    });
    
    await step('User sends message', async () => {
      const button = canvas.getByRole('button', { name: /send/i });
      await userEvent.click(button);
    });
    
    await step('AI response streams in', async () => {
      // Wait for streaming response to appear
      await canvas.findByText(/hello/i, {}, { timeout: 3000 });
    });
  },
};
```

## Component categories

Organise components by complexity:

| Category | Description | Examples |
|----------|-------------|----------|
| **Primitives** (`components/ui/`) | Base elements from shadcn/ui | Button, Input, Card, Badge |
| **Composed** (`components/composed/`) | Application-specific combinations | UserCard, ProductCard, StatCard |
| **Patterns** (`components/patterns/`) | Complex reusable patterns | DataTable, DashboardHeader, AuthForm |
| **AI** (`components/ai/`) | AI SDK components | MessageBubble, ToolInvocation, StreamingResponse |

## Story organisation patterns

### Variant-focused stories

For components with many visual variants:

```typescript
export const AllVariants: Story = {
  render: () => (
    <div className="flex flex-col gap-4">
      <div className="flex gap-2">
        <Button variant="default">Default</Button>
        <Button variant="destructive">Destructive</Button>
        <Button variant="outline">Outline</Button>
      </div>
      <div className="flex gap-2">
        <Button size="sm">Small</Button>
        <Button size="default">Default</Button>
        <Button size="lg">Large</Button>
      </div>
    </div>
  ),
};
```

### State-focused stories

For components with multiple states:

```typescript
export const AllStates: Story = {
  render: () => (
    <div className="flex flex-col gap-4">
      <Input placeholder="Default state" />
      <Input placeholder="Disabled state" disabled />
      <Input placeholder="Error state" aria-invalid="true" />
      <Input placeholder="With value" value="Example text" onChange={() => {}} />
    </div>
  ),
};
```

### Data-focused stories

For components that render data:

```typescript
const mockUsers = [
  { id: 1, name: 'Alice', role: 'Admin' },
  { id: 2, name: 'Bob', role: 'User' },
  { id: 3, name: 'Charlie', role: 'User' },
];

export const WithData: Story = {
  args: {
    users: mockUsers,
  },
};

export const EmptyState: Story = {
  args: {
    users: [],
  },
};

export const Loading: Story = {
  args: {
    users: undefined,
    isLoading: true,
  },
};
```

## Interaction testing patterns

Use `play` functions to test complex interactions:

```typescript
import { expect, userEvent, within } from '@storybook/test';

export const FormValidation: Story = {
  play: async ({ canvasElement, step }) => {
    const canvas = within(canvasElement);
    
    await step('Submit empty form', async () => {
      const submit = canvas.getByRole('button', { name: /submit/i });
      await userEvent.click(submit);
    });
    
    await step('Verify validation errors', async () => {
      expect(await canvas.findByText(/email is required/i)).toBeInTheDocument();
      expect(await canvas.findByText(/password is required/i)).toBeInTheDocument();
    });
    
    await step('Fill valid data', async () => {
      await userEvent.type(
        canvas.getByLabelText(/email/i),
        'user@example.com'
      );
      await userEvent.type(
        canvas.getByLabelText(/password/i),
        'password123'
      );
    });
    
    await step('Submit valid form', async () => {
      const submit = canvas.getByRole('button', { name: /submit/i });
      await userEvent.click(submit);
    });
  },
};
```

## Mock Service Worker (MSW) integration

For components that fetch data:

```typescript
import { http, HttpResponse } from 'msw';

const meta = {
  title: 'Components/UserList',
  component: UserList,
  parameters: {
    msw: {
      handlers: [
        http.get('/api/users', () => {
          return HttpResponse.json([
            { id: 1, name: 'Alice' },
            { id: 2, name: 'Bob' },
          ]);
        }),
      ],
    },
  },
} satisfies Meta<typeof UserList>;

export const Default: Story = {};

export const ApiError: Story = {
  parameters: {
    msw: {
      handlers: [
        http.get('/api/users', () => {
          return HttpResponse.error();
        }),
      ],
    },
  },
};
```

## Accessibility requirements

Every component must:

1. **Use semantic HTML** — `<button>` not `<div onClick>`
2. **Support keyboard navigation** — Tab, Enter, Escape
3. **Include ARIA attributes** — `aria-label`, `aria-expanded`, `aria-busy`
4. **Have sufficient colour contrast** — Pass WCAG AA (4.5:1 for text)
5. **Support screen readers** — All interactive elements labeled
6. **Show focus indicators** — Visible focus rings on all interactive elements

Run the a11y addon in Storybook and fix all violations before considering a component complete.

## Component checklist

Before marking a component as complete:

- [ ] Story file created with all meaningful variants
- [ ] Component implementation with TypeScript
- [ ] Props documented with JSDoc
- [ ] Accessibility verified (a11y addon passes)
- [ ] Interaction tests added for stateful behaviour
- [ ] Unit tests created (optional but recommended)
- [ ] Verified in Storybook
- [ ] No console errors or warnings
- [ ] Follows project code style
- [ ] Added to component registry (if using custom gallery)

## Hand off

When component is complete:

> "Component created: [Name]
>
> - Stories: X variants + Y interaction tests
> - Location: `components/[category]/[name]/`
> - View in Storybook: http://localhost:6006/?path=/story/[path]
>
> Next steps:
> - Use **component-testing** skill to add visual regression tests
> - Import into your application: `import { ComponentName } from '@/components/[category]/[name]'`"

## Principles

- **Stories first, component second** — TDD for UI
- **Isolation** — Components developed independently of the application
- **Variants over configuration** — Use CVA for systematic variant patterns
- **Composition over props** — Compound components with Context for complex state
- **Accessibility by default** — WCAG compliance, keyboard navigation, ARIA
- **Test behaviour, not implementation** — User-centric testing with RTL patterns
- **Documentation from code** — JSDoc generates Storybook documentation automatically
