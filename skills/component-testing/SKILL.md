---
name: component-testing
description: >-
  Add comprehensive testing to UI components including interaction tests, visual regression,
  accessibility audits, and unit tests. Use after component-development to ensure components
  are production-ready, or when the user asks to test components, add visual regression tests,
  or verify accessibility compliance.
---

# Component Testing

Add comprehensive testing to UI components: interaction tests, visual regression testing, accessibility audits, and unit tests. Ensures components are production-ready and regression-proof.

Announce at start: "I'm using the component-testing skill to add comprehensive tests to your components."

## Testing layers

Modern component testing uses multiple complementary approaches:

| Layer | Tool | What it tests | When it runs |
|-------|------|---------------|--------------|
| **Interaction** | Storybook play functions | User interactions, state changes, events | Dev + CI |
| **Visual regression** | Chromatic / Playwright | Pixel-perfect appearance across browsers | CI (on PR) |
| **Accessibility** | @storybook/addon-a11y + axe-core | WCAG compliance, ARIA, keyboard nav | Dev + CI |
| **Unit** | React Testing Library + Vitest | Component logic, edge cases | Dev + CI |
| **Integration** | React Testing Library + MSW | Multi-component flows with API mocking | CI |

This skill focuses on the first three (interaction, visual, accessibility). For integration testing, use the **end-to-end-testing** skill.

## Prerequisites

1. **Component exists** — Component and story file created (use **component-development** skill)
2. **Storybook running** — Can view component at http://localhost:6006
3. **Test runner installed** — Storybook test runner for CI execution
4. **Design system available** — `docs/design/design-system.md` exists for UI projects; tests should verify token, accessibility, and state requirements from it

If test runner is not installed:

```bash
yarn add -D @storybook/test-runner playwright
npx playwright install --with-deps chromium
```

## Testing workflow

### Step 1: Interaction testing with play functions

Interaction tests simulate user behaviour inside stories using `@storybook/test`.

**When to add interaction tests:**

- Component has stateful behaviour (expand/collapse, show/hide)
- Component handles user input (forms, searches, filters)
- Component has multi-step flows (wizards, onboarding)
- Component calls callbacks on interaction (onClick, onChange)

**Example: Form validation interaction test**

```typescript
import { expect, userEvent, within, waitFor } from '@storybook/test';
import type { Meta, StoryObj } from '@storybook/react';
import { LoginForm } from './login-form';

const meta = {
  title: 'Patterns/LoginForm',
  component: LoginForm,
} satisfies Meta<typeof LoginForm>;

export default meta;
type Story = StoryObj<typeof meta>;

/**
 * Test: Form validation - empty fields
 */
export const ValidationErrors: Story = {
  play: async ({ canvasElement, step }) => {
    const canvas = within(canvasElement);
    
    await step('Submit form without filling fields', async () => {
      const submitButton = canvas.getByRole('button', { name: /sign in/i });
      await userEvent.click(submitButton);
    });
    
    await step('Verify validation errors appear', async () => {
      await waitFor(async () => {
        expect(await canvas.findByText(/email is required/i)).toBeInTheDocument();
        expect(await canvas.findByText(/password is required/i)).toBeInTheDocument();
      });
    });
  },
};

/**
 * Test: Successful form submission
 */
export const SuccessfulSubmission: Story = {
  play: async ({ canvasElement, step }) => {
    const canvas = within(canvasElement);
    
    await step('Fill in email', async () => {
      const emailInput = canvas.getByLabelText(/email/i);
      await userEvent.type(emailInput, 'user@example.com');
    });
    
    await step('Fill in password', async () => {
      const passwordInput = canvas.getByLabelText(/password/i);
      await userEvent.type(passwordInput, 'SecurePass123!');
    });
    
    await step('Submit form', async () => {
      const submitButton = canvas.getByRole('button', { name: /sign in/i });
      await userEvent.click(submitButton);
    });
    
    await step('Verify success message', async () => {
      await waitFor(async () => {
        expect(await canvas.findByText(/signed in successfully/i)).toBeInTheDocument();
      });
    });
  },
};

/**
 * Test: Keyboard navigation
 */
export const KeyboardNavigation: Story = {
  play: async ({ canvasElement, step }) => {
    const canvas = within(canvasElement);
    
    await step('Tab to email input', async () => {
      const emailInput = canvas.getByLabelText(/email/i);
      emailInput.focus();
      expect(emailInput).toHaveFocus();
    });
    
    await step('Tab to password input', async () => {
      await userEvent.tab();
      const passwordInput = canvas.getByLabelText(/password/i);
      expect(passwordInput).toHaveFocus();
    });
    
    await step('Tab to submit button', async () => {
      await userEvent.tab();
      const submitButton = canvas.getByRole('button', { name: /sign in/i });
      expect(submitButton).toHaveFocus();
    });
    
    await step('Submit with Enter key', async () => {
      await userEvent.keyboard('{Enter}');
      // Form submission would be verified here
    });
  },
};
```

**Interaction testing best practices:**

- **Use `step()` for organisation** — Groups related actions, creates closure boundaries
- **Query by role first** — `getByRole('button')` not `getByTestId('submit-btn')`
- **Wait for async changes** — Use `waitFor` and `findBy*` queries
- **Test real user flows** — Don't just click buttons, simulate complete workflows
- **Verify side effects** — Check DOM changes, not internal state

**Run interaction tests:**

```bash
# In Storybook UI (dev mode)
yarn storybook
# Tests run automatically, results shown in UI

# In CI (headless)
yarn test-storybook
```

### Step 2: Visual regression testing

Visual regression tests catch unintended UI changes by comparing screenshots.

**Option A: Chromatic (Recommended, cloud-based)**

Chromatic is the official Storybook visual testing service.

**Setup:**

```bash
yarn add -D chromatic
```

Add to `package.json`:

```json
{
  "scripts": {
    "chromatic": "chromatic --project-token=<your-token>"
  }
}
```

**Configure snapshots:**

```typescript
// In individual stories
export const Default: Story = {
  parameters: {
    chromatic: {
      // Disable for stories that shouldn't be snapshotted
      disableSnapshot: false,
      
      // Pause animations before snapshot
      pauseAnimationAtEnd: true,
      
      // Wait for fonts/images to load
      delay: 300,
    },
  },
};

// For all stories in a file
const meta = {
  title: 'UI/Button',
  component: Button,
  parameters: {
    chromatic: {
      viewports: [320, 1200], // Test mobile and desktop
    },
  },
} satisfies Meta<typeof Button>;
```

**Run visual tests:**

```bash
# Local (uploads to Chromatic)
yarn chromatic

# CI (in GitHub Actions)
# Chromatic will comment on PRs with visual diff results
```

**Option B: Playwright (Self-hosted)**

For self-hosted visual regression testing:

```bash
yarn add -D @playwright/test
```

Create `tests/visual.spec.ts`:

```typescript
import { test, expect } from '@playwright/test';

test.describe('Visual regression tests', () => {
  test('Button component - all variants', async ({ page }) => {
    await page.goto('http://localhost:6006/?path=/story/ui-button--all-variants');
    
    // Wait for Storybook to load
    await page.waitForSelector('#storybook-preview-iframe');
    
    // Switch to iframe
    const frame = page.frameLocator('#storybook-preview-iframe');
    
    // Wait for component to render
    await frame.locator('[data-testid="story-root"]').waitFor();
    
    // Take screenshot
    await expect(frame.locator('[data-testid="story-root"]')).toHaveScreenshot(
      'button-all-variants.png'
    );
  });
  
  test('LoginForm - validation errors', async ({ page }) => {
    await page.goto('http://localhost:6006/?path=/story/patterns-loginform--validation-errors');
    const frame = page.frameLocator('#storybook-preview-iframe');
    
    // Play function runs automatically
    await frame.locator('text=Email is required').waitFor();
    
    await expect(frame.locator('[data-testid="story-root"]')).toHaveScreenshot(
      'login-form-validation-errors.png'
    );
  });
});
```

Add to `package.json`:

```json
{
  "scripts": {
    "test:visual": "playwright test tests/visual.spec.ts",
    "test:visual:update": "playwright test tests/visual.spec.ts --update-snapshots"
  }
}
```

**Run visual tests:**

```bash
# Run tests
yarn test:visual

# Update baseline snapshots
yarn test:visual:update
```

### Step 3: Accessibility testing

Accessibility testing ensures WCAG compliance and keyboard navigation.

**Storybook a11y addon (real-time)**

Already installed with `@storybook/addon-essentials`. View violations in the Accessibility panel.

**Run automated a11y tests in CI:**

```typescript
// Add to existing story file
import { expect, within } from '@storybook/test';
import { injectAxe, checkA11y } from 'axe-playwright';

/**
 * Accessibility test story
 */
export const AccessibilityCheck: Story = {
  parameters: {
    a11y: {
      // Custom axe-core configuration
      config: {
        rules: [
          {
            id: 'color-contrast',
            enabled: true,
          },
        ],
      },
    },
  },
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);
    
    // Verify all buttons have accessible names
    const buttons = canvas.getAllByRole('button');
    buttons.forEach(button => {
      expect(button).toHaveAccessibleName();
    });
    
    // Verify form inputs have labels
    const inputs = canvas.getAllByRole('textbox');
    inputs.forEach(input => {
      expect(input).toHaveAccessibleName();
    });
  },
};
```

**Accessibility testing checklist:**

- [ ] All interactive elements have accessible names
- [ ] All images have alt text
- [ ] Colour contrast meets WCAG AA (4.5:1 for text)
- [ ] Keyboard navigation works (Tab, Enter, Escape)
- [ ] Focus indicators are visible
- [ ] Form inputs have associated labels
- [ ] Error messages are announced to screen readers
- [ ] Landmark roles are used correctly (`main`, `nav`, `aside`)
- [ ] Motion respects `prefers-reduced-motion` where animation is present
- [ ] Touch targets meet the design-system minimum size

**Test keyboard navigation manually:**

1. Tab through component — all interactive elements reachable
2. Shift+Tab backwards — reverse order works
3. Enter/Space on buttons — triggers action
4. Escape on modals/popovers — closes overlay
5. Arrow keys on select/combobox — navigates options

### Step 4: Unit testing with React Testing Library

For complex logic or edge cases, add unit tests:

**Create `component.test.tsx`:**

```typescript
import { render, screen, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { LoginForm } from './login-form';

describe('LoginForm', () => {
  it('renders email and password inputs', () => {
    render(<LoginForm />);
    
    expect(screen.getByLabelText(/email/i)).toBeInTheDocument();
    expect(screen.getByLabelText(/password/i)).toBeInTheDocument();
  });
  
  it('validates required fields on submit', async () => {
    const user = userEvent.setup();
    render(<LoginForm />);
    
    const submitButton = screen.getByRole('button', { name: /sign in/i });
    await user.click(submitButton);
    
    expect(await screen.findByText(/email is required/i)).toBeInTheDocument();
    expect(await screen.findByText(/password is required/i)).toBeInTheDocument();
  });
  
  it('validates email format', async () => {
    const user = userEvent.setup();
    render(<LoginForm />);
    
    const emailInput = screen.getByLabelText(/email/i);
    await user.type(emailInput, 'invalid-email');
    
    const submitButton = screen.getByRole('button', { name: /sign in/i });
    await user.click(submitButton);
    
    expect(await screen.findByText(/invalid email format/i)).toBeInTheDocument();
  });
  
  it('calls onSubmit with form data on valid submission', async () => {
    const user = userEvent.setup();
    const handleSubmit = jest.fn();
    render(<LoginForm onSubmit={handleSubmit} />);
    
    await user.type(screen.getByLabelText(/email/i), 'user@example.com');
    await user.type(screen.getByLabelText(/password/i), 'SecurePass123!');
    await user.click(screen.getByRole('button', { name: /sign in/i }));
    
    await waitFor(() => {
      expect(handleSubmit).toHaveBeenCalledWith({
        email: 'user@example.com',
        password: 'SecurePass123!',
      });
    });
  });
  
  it('disables submit button during submission', async () => {
    const user = userEvent.setup();
    const handleSubmit = jest.fn(() => new Promise(resolve => setTimeout(resolve, 1000)));
    render(<LoginForm onSubmit={handleSubmit} />);
    
    await user.type(screen.getByLabelText(/email/i), 'user@example.com');
    await user.type(screen.getByLabelText(/password/i), 'SecurePass123!');
    
    const submitButton = screen.getByRole('button', { name: /sign in/i });
    await user.click(submitButton);
    
    expect(submitButton).toBeDisabled();
  });
});
```

**Unit testing best practices:**

- **Query priority:** `getByRole` > `getByLabelText` > `getByText` > `getByTestId`
- **Use userEvent, not fireEvent** — More realistic event sequences
- **Test user behaviour, not implementation** — What users see, not internal state
- **Avoid manual `act()` wrappers** — RTL handles this automatically
- **Wait for async changes** — Use `waitFor` and `findBy*` queries

**Run unit tests:**

```bash
# Watch mode (development)
yarn test --watch

# CI mode (run once)
yarn test --run
```

### Step 5: Mock Service Worker (MSW) for API mocking

For components that fetch data, use MSW to mock API responses:

**Install MSW:**

```bash
yarn add -D msw
```

**Create mock handlers:**

```typescript
// src/mocks/handlers.ts
import { http, HttpResponse } from 'msw';

export const handlers = [
  http.get('/api/users', () => {
    return HttpResponse.json([
      { id: 1, name: 'Alice', role: 'Admin' },
      { id: 2, name: 'Bob', role: 'User' },
    ]);
  }),
  
  http.post('/api/login', async ({ request }) => {
    const body = await request.json();
    
    if (body.email === 'user@example.com' && body.password === 'password') {
      return HttpResponse.json({ token: 'fake-jwt-token' });
    }
    
    return HttpResponse.json(
      { error: 'Invalid credentials' },
      { status: 401 }
    );
  }),
];
```

**Use in Storybook:**

```typescript
// .storybook/preview.ts
import { initialize, mswLoader } from 'msw-storybook-addon';
import { handlers } from '../src/mocks/handlers';

initialize({
  onUnhandledRequest: 'warn',
});

export const loaders = [mswLoader];

export default {
  parameters: {
    msw: {
      handlers,
    },
  },
};
```

**Override in specific stories:**

```typescript
import { http, HttpResponse } from 'msw';

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

export const EmptyState: Story = {
  parameters: {
    msw: {
      handlers: [
        http.get('/api/users', () => {
          return HttpResponse.json([]);
        }),
      ],
    },
  },
};
```

**Use in unit tests:**

```typescript
import { setupServer } from 'msw/node';
import { handlers } from '@/mocks/handlers';

const server = setupServer(...handlers);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());

it('displays users from API', async () => {
  render(<UserList />);
  
  expect(await screen.findByText('Alice')).toBeInTheDocument();
  expect(await screen.findByText('Bob')).toBeInTheDocument();
});

it('handles API error gracefully', async () => {
  server.use(
    http.get('/api/users', () => {
      return HttpResponse.error();
    })
  );
  
  render(<UserList />);
  
  expect(await screen.findByText(/failed to load users/i)).toBeInTheDocument();
});
```

## CI integration

### GitHub Actions workflow

Create `.github/workflows/component-tests.yml`:

```yaml
name: Component Tests

on:
  pull_request:
    branches: [main]

jobs:
  interaction-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      
      - name: Install dependencies
        run: yarn install --frozen-lockfile
      
      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium
      
      - name: Build Storybook
        run: yarn build-storybook --quiet
      
      - name: Run interaction tests
        run: yarn test-storybook
  
  visual-regression:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0 # Chromatic needs full git history
      
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      
      - name: Install dependencies
        run: yarn install --frozen-lockfile
      
      - name: Run Chromatic
        uses: chromaui/action@latest
        with:
          projectToken: ${{ secrets.CHROMATIC_PROJECT_TOKEN }}
          exitZeroOnChanges: true # Don't fail on visual changes, just report
  
  unit-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      
      - name: Install dependencies
        run: yarn install --frozen-lockfile
      
      - name: Run unit tests
        run: yarn test --run
```

## Testing strategies by component type

### Primitive components (Button, Input, Badge)

**Focus:** Visual variants, states, accessibility

```typescript
// Test all visual variants
export const AllVariants: Story = { /* ... */ };

// Test disabled state
export const Disabled: Story = { /* ... */ };

// Test keyboard navigation
export const KeyboardNav: Story = {
  play: async ({ canvasElement }) => {
    // Tab, Enter, Space tests
  },
};
```

### Extended components

**Focus:** Backwards compatibility and regression safety

When an existing component is extended with new variants, sizes, or optional props:

- Re-run every existing story for that component
- Add stories for the new variant/state
- Add visual regression coverage comparing default rendering against the previous baseline
- Run codebase usage tests for existing props and default behaviour
- Confirm new props are optional and do not change default rendering

```typescript
export const DefaultBackwardsCompatibility: Story = {
  args: {
    children: 'Existing default',
  },
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);
    expect(canvas.getByRole('button', { name: /existing default/i })).toBeEnabled();
  },
};
```

### Design-system conformance tests

**Focus:** Token use, theming, and brand consistency

- Verify light/dark or supported theme states
- Snapshot typography, spacing, and state variants
- Ensure semantic states exist for success, warning, error, disabled, loading, empty, and focus where applicable
- Fail or flag hardcoded colours and styles during lint/review when tooling supports it

### Form components (Input, Select, Checkbox)

**Focus:** Validation, controlled/uncontrolled state, error handling

```typescript
// Test validation
export const ValidationError: Story = {
  play: async ({ canvasElement }) => {
    // Submit invalid data, verify error message
  },
};

// Test controlled state
export const ControlledInput: Story = {
  render: () => {
    const [value, setValue] = useState('');
    return <Input value={value} onChange={e => setValue(e.target.value)} />;
  },
};
```

### Data components (Table, List, Card)

**Focus:** Loading states, empty states, error states, data rendering

```typescript
// Test with data
export const WithData: Story = {
  args: { items: mockData },
};

// Test loading state
export const Loading: Story = {
  args: { items: undefined, loading: true },
};

// Test empty state
export const Empty: Story = {
  args: { items: [] },
};

// Test error state
export const Error: Story = {
  args: { error: 'Failed to load data' },
};
```

### Interactive components (Modal, Dropdown, Accordion)

**Focus:** Open/close behaviour, keyboard navigation, focus management

```typescript
// Test open/close
export const OpenClose: Story = {
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);
    
    // Click trigger to open
    await userEvent.click(canvas.getByRole('button'));
    expect(canvas.getByRole('dialog')).toBeInTheDocument();
    
    // Press Escape to close
    await userEvent.keyboard('{Escape}');
    expect(canvas.queryByRole('dialog')).not.toBeInTheDocument();
  },
};
```

### AI components (Chat, MessageBubble, StreamingResponse)

**Focus:** Streaming states, tool invocations, error handling, multi-step flows

```typescript
// Test streaming state
export const Streaming: Story = {
  parameters: {
    msw: {
      handlers: [
        // Mock streaming API
      ],
    },
  },
  play: async ({ canvasElement }) => {
    // Verify streaming indicator appears
    // Wait for stream to complete
    // Verify final message
  },
};
```

## Component testing checklist

Before marking component as production-ready:

- [ ] **Interaction tests** — All user flows covered with play functions
- [ ] **Visual regression** — Chromatic/Playwright snapshots configured
- [ ] **Accessibility** — No violations in a11y addon
- [ ] **Keyboard navigation** — All interactions work with keyboard
- [ ] **Responsive design** — Tested at mobile and desktop viewports
- [ ] **Loading states** — Loading, empty, error states tested
- [ ] **Edge cases** — Long text, missing data, extreme values handled
- [ ] **Unit tests** — Complex logic covered with RTL tests
- [ ] **CI integration** — Tests run on every PR
- [ ] **Documentation** — All variants documented in stories

## Hand off

When testing is complete:

> "Component testing complete: [Component Name]
>
> ✅ Interaction tests: X stories with play functions
> ✅ Visual regression: Configured with [Chromatic/Playwright]
> ✅ Accessibility: No violations (WCAG AA compliant)
> ✅ Unit tests: X test cases passing
>
> CI integration:
> - Interaction tests run on every commit
> - Visual regression tests run on every PR
> - Accessibility checks automatic in Storybook
>
> View test results: http://localhost:6006"

## Principles

- **Multiple layers** — Interaction + visual + accessibility + unit tests
- **User-centric** — Test what users see and do, not implementation details
- **Automated** — All tests run in CI, no manual testing required
- **Visual proof** — Screenshots catch regressions code tests miss
- **Accessibility first** — WCAG compliance verified automatically
- **Fast feedback** — Interaction tests run in seconds during development
