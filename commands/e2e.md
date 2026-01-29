---
description: Generate and run end-to-end tests with Playwright. Creates test journeys, runs tests, captures screenshots/videos/traces, and uploads artifacts.
disable-model-invocation: true
---

# E2E Command

This command invokes the **e2e-runner** agent to generate, maintain, and execute end-to-end tests using Playwright.

## What This Command Does

1. **Generate Test Journeys** - Create Playwright tests for user flows
2. **Run E2E Tests** - Execute tests across browsers
3. **Capture Artifacts** - Screenshots, videos, traces on failures
4. **Upload Results** - HTML reports and JUnit XML
5. **Identify Flaky Tests** - Quarantine unstable tests

## When to Use

Use `/omnipowers:e2e` when:
- Testing critical user journeys (Agent creation, editing, execution)
- Verifying multi-step flows work end-to-end
- Testing UI interactions and navigation
- Validating integration between frontend and backend
- Preparing for production deployment

## How It Works

The e2e-runner agent will:

1. **Analyze user flow** and identify test scenarios
2. **Generate Playwright test** using Page Object Model pattern
3. **Run tests** across multiple browsers (Chrome, Firefox, Safari)
4. **Capture failures** with screenshots, videos, and traces
5. **Generate report** with results and artifacts
6. **Identify flaky tests** and recommend fixes

## Example Usage

```
User: /omnipowers:e2e Test the agent creation flow

Agent (e2e-runner):
# E2E Test Generation: Agent Creation Flow

## Test Scenario Identified

**User Journey:** View Agent List → Click Create → Fill Form → Save → Verify in List

**Test Coverage:**
1. Navigate to agents list page
2. Click "Create Agent" button
3. Fill in agent name, description, and model
4. Submit form
5. Verify redirection to agent list
6. Verify new agent appears in the list

## Generated Test Code

```typescript
// tests/e2e/agents/create-agent.spec.ts
import { test, expect } from '@playwright/test'
import { AgentsPage } from '../../pages/AgentsPage'
import { CreateAgentPage } from '../../pages/CreateAgentPage'

test.describe('Agent Creation Flow', () => {
  test('user can create a new agent', async ({ page }) => {
    // 1. Navigate to agents list page
    const agentsPage = new AgentsPage(page)
    await agentsPage.goto()
    
    // 2. Click "Create Agent" button
    await agentsPage.createButton.click()
    
    // 3. Fill in agent details
    const createPage = new CreateAgentPage(page)
    await createPage.nameInput.fill('Test Agent 001')
    await createPage.descriptionInput.fill('A test agent for E2E')
    await createPage.selectModel('claude-3-opus')
    
    // 4. Submit form
    await createPage.submitButton.click()
    
    // 5. Verify redirection
    await expect(page).toHaveURL(/\/agents$/)
    
    // 6. Verify new agent appears
    await expect(agentsPage.getAgentCard('Test Agent 001')).toBeVisible()
    
    // Take screenshot
    await page.screenshot({ path: 'artifacts/agent-created.png' })
  })
})
```

## Running Tests

```bash
# Run the generated test
npx playwright test tests/e2e/agents/create-agent.spec.ts
```

## Critical Flows

Prioritize these E2E tests for the Omnipowers platform:

**🔴 CRITICAL (Must Always Pass):**
1. User can view Agent List
2. User can create a new Agent
3. User can edit an existing Agent
4. User can delete an Agent
5. Agent chat interface loads/responds

**🟡 IMPORTANT:**
1. Agent filtering/searching
2. Skill configuration
3. Theme switching (Dark/Light mode)
4. Mobile responsiveness

## Best Practices

**DO:**
- ✅ Use Page Object Model for maintainability
- ✅ Use data-testid attributes for selectors
- ✅ Wait for API responses, not arbitrary timeouts
- ✅ Test critical user journeys end-to-end
- ✅ Run tests before merging to main
- ✅ Review artifacts when tests fail

**DON'T:**
- ❌ Use brittle selectors (CSS classes can change)
- ❌ Test implementation details
- ❌ Run tests against production
- ❌ Ignore flaky tests
- ❌ Skip artifact review on failures
- ❌ Test every edge case with E2E (use unit tests)

## Integration with Other Commands

- Use `/omnipowers:plan` to identify critical journeys to test
- Use `/omnipowers:tdd` for unit tests (faster, more granular)
- Use `/omnipowers:e2e` for integration and user journey tests
- Use `/omnipowers:code-review` to verify test quality

## Related Agents

This command invokes the `e2e-runner` agent located at:
`~/.claude/agents/e2e-runner.md`

## Quick Commands

```bash
# Run all E2E tests
npx playwright test

# Run specific test file
npx playwright test tests/e2e/agents/create-agent.spec.ts

# Run in headed mode (see browser)
npx playwright test --headed

# Debug test
npx playwright test --debug

# Generate test code
npx playwright codegen http://localhost:3000

# View report
npx playwright show-report
```
