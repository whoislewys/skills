---
name: unit-test
description: Guidance for writing unit tests that verify observable behavior and public contracts rather than internal implementation. Use when writing, reviewing, or refactoring unit tests, deciding whether to mock or stub, or when tests are brittle and break on refactors.
---

## Unit Testing: Test Behavior, Not Implementation

### Core Philosophy: The Pragmatic XP Approach

Effective unit testing focuses on **what** the code achieves (its behavior and public contract) rather than **how** it achieves it (its internal implementation). The primary goal of a test suite is to provide a safety net for fearless refactoring while ensuring reliability. When tests are tightly coupled to internal code structures, they become a liability—breaking during minor refactors even when the application still works perfectly. By shifting the focus to observable state changes and inputs/outputs, you write resilient tests that act as living documentation.

### Exercising Every Code Path Safely

To confidently exercise every conceivable code path—including edge cases, boundary conditions, and error states—without creating brittle tests, structure your test suites around varying inputs and expected outcomes. Use data-driven testing patterns to pass diverse payloads into your public interfaces. If a specific edge case requires complex logic, don't reach for a mock to force the path; instead, construct the precise input state or leverage dependency injection to naturally guide the execution down that branch. This ensures that every code path is validated by real execution, not simulated assumptions.

---

### Why Mocking and Expecting Method Calls is Bad Practice

* **Brittle Refactoring:** If you extract logic from your service object into smaller classes, or change its name, your tests will fail even though the application's functionality works perfectly.
* **Fake Coverage:** Stubbing a service to return true hides real-world errors. Your test will pass, but the code may blow up in production when the real service fails or returns unexpected data.
* **Implementation Bias:** You test "how" the code works rather than "what the code does".

#### Better Alternatives for Testing

* **Fakes / Stubs (For Network/External API boundaries):** Instead of verifying that an external API method was called, use tools like HTTP VCR/fixtures in your unit tests to record and replay actual API responses.
* **Dependency Injection:** Pass collaborators to the service object's initializer so you can swap in simple in-memory objects or fakes in your tests.

---

### Example: Testing Behavior vs. Mocking Calls

#### Anti-Pattern: Testing the Implementation

Avoid writing tests that spy on internal method calls or verify that a specific collaborator was triggered. This binds your test tightly to the current syntax of the codebase.

```ruby
# DON'T DO THIS
it "calls the payment service" do
  expect(PaymentService).to receive(:call).with(user, amount)
  CheckoutService.new(user, amount).process
end

```

#### Best Practice: Testing the Observable State Change

Write your tests to assert that a concrete change happened (e.g., the user's balance updated, or an order record was created).

```ruby
# DO THIS
it "processes the checkout and updates the user balance" do
  expect {
    CheckoutService.new(user, amount).process
  }.to change { user.reload.balance }.by(-amount)
end

```

> **Why this works:** By asserting the final state change, you are completely free to refactor `CheckoutService` and its internal collaborators as much as you want without breaking your test suite, ensuring true agility.
