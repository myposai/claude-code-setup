---
name: playwright-testing
description: create, update, debug, and review browser automation and end-to-end tests using playwright in javascript/typescript, python, or .net. use when building test scripts, checking selectors and locators, asserting ui behavior, handling waits, screenshots, traces, downloads, auth flows, api-driven back-end flows, distributed services, database-backed workflows, transaction-based consistency checks, mobile web or hybrid app coverage, cross-browser test coverage, or debugging flaky tests.
---

# Playwright Testing Skill

Use this skill to write reliable Playwright test scripts and to improve existing browser automation and integration coverage.

## Core goal

Produce tests that are:
- stable across runs
- explicit about waits and assertions
- readable and maintainable
- compatible with JavaScript/TypeScript, Python, or .NET Playwright
- suitable for web UI, backend-integrated flows, and observable distributed systems

## Default approach

1. Prefer Playwright Test for JavaScript/TypeScript projects.
2. Prefer Playwright for .NET when the project is C#/.NET-based.
3. Use the Python Playwright API when the project is Python-based.
4. Use semantic locators first.
5. Prefer user-visible assertions over brittle DOM checks.
6. Keep tests isolated and deterministic.
7. Verify backend outcomes only through observable behavior unless direct backend assertions are explicitly required.

## Before writing a test

Identify:
- target browser(s)
- test framework: Playwright Test, raw Playwright, or Playwright for .NET
- language: JavaScript/TypeScript, Python, or C#
- auth requirements
- required test data
- whether the test should be headless or headed
- whether screenshots, traces, or videos are needed
- whether the flow depends on APIs, queues, events, background jobs, or databases
- whether the target is a web app, hybrid app, mobile web flow, or native mobile app

If native iOS/Android automation is requested, use Playwright only for the web portions of the app or browser-based flows. For fully native UI automation, note that a mobile-specific framework is usually required.

## Locator strategy

Prefer locators in this order:
1. `getByRole`
2. `getByLabel`
3. `getByPlaceholder`
4. `getByText`
5. `getByTestId`
6. CSS/XPath only when necessary

Avoid:
- brittle nth-child selectors
- long CSS chains
- XPath unless no better option exists
- selectors tied to layout or styling

## Waiting strategy

Prefer auto-waiting and explicit assertions.

Use:
- `await expect(locator).toBeVisible()`
- `await expect(locator).toHaveText(...)`
- `await expect(page).toHaveURL(...)`
- `await page.waitForLoadState('networkidle')` only when needed
- `await locator.waitFor()` only when an explicit condition is required

Avoid arbitrary sleeps such as `waitForTimeout` unless debugging or simulating delay.

## Assertion strategy

Assert the smallest useful behavior:
- element visible/hidden
- text content
- URL changes
- enabled/disabled state
- checked/selected state
- count of items
- file downloaded
- dialog appeared
- request completed
- database-backed side effects reflected in the UI or API
- eventual consistency after a transaction or distributed workflow completes

Prefer meaningful assertions over snapshot-heavy tests.

## Authentication

When auth is required:
- use a storage state file if available
- avoid logging in through the UI in every test
- isolate login setup in a dedicated helper or setup project
- keep secrets out of the test code

## Test structure

Use Arrange / Act / Assert or a similar clear structure.

Example JavaScript/TypeScript:
```ts
import { test, expect } from '@playwright/test'

test('can create a task', async ({ page }) => {
  await page.goto('/tasks')

  await page.getByRole('button', { name: 'New task' }).click()
  await page.getByLabel('Title').fill('Review PR')
  await page.getByRole('button', { name: 'Save' }).click()

  await expect(page.getByText('Review PR')).toBeVisible()
})