## Test coverage best practices

- **Write Minimal Tests During Development**: Do NOT write tests for every change or intermediate step. Focus on completing the feature implementation first, then add strategic tests only at logical completion points
- **Test Only Core User Flows**: Write tests exclusively for critical paths and primary user workflows. Skip writing tests for non-critical utilities and secondary workflows until if/when you're instructed to do so.
- **Defer Edge Case Testing**: Do NOT test edge cases, error states, or validation logic unless they are business-critical. These can be addressed in dedicated testing phases, not during feature development.
- **Test Behavior, Not Implementation**: Focus tests on what the code does, not how it does it, to reduce brittleness
- **Clear Test Names**: Use descriptive names that explain what's being tested and the expected outcome
- **Mock External Dependencies**: Isolate units by mocking databases, APIs, file systems, and other external services
- **Fast Execution**: Keep unit tests fast (milliseconds) so developers run them frequently during development

## Universal Testing Principles

### Test Organization
- **Logical Grouping**: Organize tests by feature, module, or component to improve maintainability
- **Test Structure**: Follow a consistent structure (arrange, act, assert) across all tests
- **Test Independence**: Ensure tests can run in any order and don't depend on each other
- **Test Isolation**: Each test should be independent and not rely on shared state

### Test Types and Levels
- **Unit Tests**: Test individual functions, methods, or components in isolation
- **Integration Tests**: Test interactions between multiple components or modules
- **System Tests**: Test complete workflows or end-to-end scenarios
- **Test Pyramid**: Maintain a balance with more unit tests than integration tests, and more integration tests than system tests

### Test Quality
- **Readable Tests**: Write tests that serve as documentation of expected behavior
- **Single Responsibility**: Each test should verify one specific behavior or outcome
- **Deterministic Tests**: Tests should produce the same results every time they run
- **Meaningful Assertions**: Use clear assertions that reveal what went wrong when tests fail

### Test Maintenance
- **Keep Tests Updated**: Update tests when requirements change, not just when they break
- **Remove Obsolete Tests**: Delete tests for features that no longer exist
- **Refactor Test Code**: Apply the same quality standards to test code as production code
- **Test Documentation**: Document complex test scenarios and the reasoning behind them

### Testing Strategy
- **Risk-Based Testing**: Prioritize testing based on business risk and impact
- **Regression Testing**: Ensure new changes don't break existing functionality
- **Test Coverage Goals**: Aim for meaningful coverage of critical paths rather than arbitrary percentage targets
- **Continuous Testing**: Integrate testing into the development workflow, not as an afterthought
