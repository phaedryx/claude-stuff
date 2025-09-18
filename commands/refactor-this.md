# Refactor-This Command

**Command**: `/refactor-this <file_path>`

**Description**: Improves existing code quality, structure, and maintainability for a specific file using Claude's specialized code-refactorer agent with risk-based refactoring strategies.

## What This Command Does

When `/refactor-this <file_path>` is executed, Claude will:

1. **Project Context Analysis**:
   - Read project standards from CLAUDE.md, .cursor/rules/, and *.ai.md files
   - Understand coding conventions and architectural patterns
   - Identify project-specific refactoring constraints and preferences

2. **Risk Assessment**:
   - Evaluate the refactoring risk level (High, Medium, Low)
   - Analyze test coverage, dependencies, and business criticality
   - Determine appropriate refactoring strategy based on risk profile

3. **Code Analysis**:
   - Identify code smells, anti-patterns, and improvement opportunities
   - Assess adherence to SOLID principles and design patterns
   - Evaluate current structure, complexity, and maintainability

4. **Test-Driven Refactoring**:
   - Write characterization tests to capture current behavior
   - Implement incremental improvements with validation at each step
   - Ensure functionality preservation throughout the process

5. **Quality Improvement**:
   - Apply clean code principles and project standards
   - Improve readability, maintainability, and performance
   - Remove technical debt while preserving business logic

## Usage Examples

```
/refactor-this app/services/legacy_payment_processor.rb
```

Refactors a legacy service class with risk-appropriate strategies and comprehensive testing.

```
/refactor-this frontend/components/UserDashboard.tsx
```

Improves a React component's structure, hooks usage, and maintainability.

```
/refactor-this app/models/user.rb
```

Cleans up a model class by improving validations, associations, and method organization.

## Refactoring Process

### 1. Risk Assessment Phase
```
= Analyzing: app/services/legacy_payment_processor.rb

=Ê Risk Assessment:
  Risk Level: =¨ HIGH RISK
  Rationale: Legacy code with limited test coverage, external API dependencies, 
            production-critical payment processing
  Strategy: Characterization tests first, minimal incremental changes, extensive validation
```

### 2. Current State Analysis
```
=Ë Current State Analysis:
  File Size: 450 lines (exceeds recommended 200-line limit)
  Complexity: High (cyclomatic complexity: 15, recommended: <10)
  Test Coverage: 35% (insufficient for safe refactoring)
  
  Code Smells Identified:
   Long method: #process_payment (85 lines) - violates SRP
   Duplicate code: Error handling repeated 4 times
   Magic numbers: Timeout values hardcoded (30, 60, 120)
   God object: Handles payment, logging, notifications, and validation
   Deep nesting: 5-level conditional nesting in #validate_card
```

### 3. Refactoring Plan
```
<¯ Refactoring Plan:
  
  Phase 1: Safety Net (HIGH RISK approach)
  1. Write characterization tests for all public methods
  2. Add integration tests for payment gateway interactions
  3. Document current behavior and edge cases
  
  Phase 2: Structural Improvements
  4. Extract payment validation into PaymentValidator class
  5. Extract notification logic into PaymentNotifier service
  6. Replace magic numbers with named constants
  7. Simplify conditional logic with guard clauses
  
  Phase 3: Method Refactoring
  8. Break down #process_payment into smaller, focused methods
  9. Extract duplicate error handling into private methods
  10. Improve naming to reflect business intent
```

### 4. Implementation with Validation
```
=' Implementation:

Step 1: Characterization Tests 
  Created: spec/services/legacy_payment_processor_spec.rb
  Coverage: Added tests for all public methods and edge cases
  Validation: All tests passing, current behavior captured

Step 2: Extract PaymentValidator   
  Created: app/services/payment_validator.rb
  Changes: Moved validation logic from main class
  Validation: Tests passing, functionality preserved

Step 3: Replace Magic Numbers 
  Constants: Added TIMEOUT_SHORT = 30, TIMEOUT_MEDIUM = 60, TIMEOUT_LONG = 120
  Changes: Replaced 6 hardcoded timeout values
  Validation: Tests passing, behavior unchanged
```

## Risk-Based Strategies

### =¨ High Risk Files
- **Legacy code with no tests**: Write comprehensive characterization tests first
- **Production-critical systems**: Minimal changes with extensive validation
- **External dependencies**: Map all integration points before refactoring
- **Complex business logic**: Preserve existing behavior exactly

###   Medium Risk Files  
- **Moderate test coverage**: Add missing tests for uncovered scenarios
- **Multiple integrations**: Refactor in small, isolated chunks
- **Performance-sensitive**: Monitor performance impact of changes
- **Active development**: Coordinate with team to avoid conflicts

###  Low Risk Files
- **Well-tested code**: Bold refactoring with confidence
- **Isolated components**: Focus on design improvements and patterns
- **Recent additions**: Apply modern practices and conventions
- **Utility functions**: Optimize for reusability and clarity

## Common Refactoring Patterns

### Method Extraction
```ruby
# Before: Long method with multiple responsibilities
def process_payment(amount, card, user)
  # 50 lines of validation, processing, and notification logic
end

# After: Extracted into focused methods
def process_payment(amount, card, user)
  validate_payment_request(amount, card, user)
  charge_result = charge_payment_gateway(amount, card)
  record_transaction(charge_result, user)
  notify_payment_completion(charge_result, user)
  charge_result
end
```

### Guard Clauses
```ruby
# Before: Deep nesting
def validate_card(card)
  if card.present?
    if card.valid?
      if card.not_expired?
        # validation logic
      else
        raise CardExpiredError
      end
    else
      raise InvalidCardError
    end
  else
    raise MissingCardError
  end
end

# After: Guard clauses
def validate_card(card)
  raise MissingCardError unless card.present?
  raise InvalidCardError unless card.valid?
  raise CardExpiredError if card.expired?
  
  # validation logic
end
```

### Constant Extraction
```ruby
# Before: Magic numbers
def retry_payment(payment)
  sleep(30) if attempt < 3
  timeout_after(120) { gateway.charge(payment) }
end

# After: Named constants
RETRY_DELAY_SECONDS = 30
MAX_RETRY_ATTEMPTS = 3  
GATEWAY_TIMEOUT_SECONDS = 120

def retry_payment(payment)
  sleep(RETRY_DELAY_SECONDS) if attempt < MAX_RETRY_ATTEMPTS
  timeout_after(GATEWAY_TIMEOUT_SECONDS) { gateway.charge(payment) }
end
```

## Error Handling and Edge Cases

### File Not Found
```
L File not found: app/services/nonexistent.rb
=¡ Check the file path and ensure the file exists in the repository.
```

### Non-Code Files
```
9  File type not suitable for refactoring: config.json
This command is designed for code files (.rb, .js, .ts, .py, etc.)
Consider using configuration-specific tools for JSON/YAML files.
```

### Already Well-Structured Code
```
 Code analysis complete: app/models/user.rb
=Ê Assessment: Code is already well-structured with good test coverage
<¯ Minor improvements suggested:
  - Consider extracting email validation regex to constant
  - Add documentation for complex scope methods
  
=¡ This file demonstrates good practices and doesn't require major refactoring.
```

### High-Risk Refactoring Warnings
```
   HIGH RISK refactoring detected for: app/services/billing_processor.rb
=¨ Critical considerations:
  - No existing test coverage (0%)
  - Production payment processing system
  - Multiple external API dependencies
  
=Ë Recommended approach:
  1. Write comprehensive characterization tests first
  2. Proceed with minimal incremental changes
  3. Extensive validation at each step
  4. Consider pair programming for critical sections
```

## Integration with Project Standards

- **Primary standards**: Rules and patterns from `.cursor/rules/` directory
- **Documentation**: Patterns and conventions from `*.ai.md` files  
- **Project context**: Architecture and practices from `CLAUDE.md`
- **Fallback**: Language and framework best practices when project standards are missing
- **Consistency**: Maintains existing code style and architectural patterns

## Output Summary Format

```
=Ê Refactoring Summary for: app/services/payment_processor.rb

 Completed Improvements:
  - Reduced method complexity from 15 to 6 (cyclomatic complexity)
  - Increased test coverage from 35% to 95%
  - Extracted 3 single-responsibility classes
  - Eliminated 4 code smell violations
  - Improved readability score from C to A

<¯ Benefits Achieved:
  - Enhanced maintainability through clear separation of concerns
  - Improved testability with focused, isolated components  
  - Reduced technical debt and future modification risk
  - Better adherence to project coding standards

=Ý Remaining Considerations:
  - Monitor performance impact in production
  - Consider adding circuit breaker pattern for external API calls
  - Update documentation to reflect new class structure
```

## When to Use This Command

- **Code review preparation**: Clean up code before submitting for review
- **Legacy code improvement**: Safely modernize old, hard-to-maintain code
- **Technical debt reduction**: Address accumulating code smells and anti-patterns
- **Performance optimization**: Improve code efficiency and resource usage
- **Standards compliance**: Align code with project conventions and best practices
- **Learning and development**: Understand and apply better coding practices

This command is particularly valuable for improving code quality while maintaining functionality, especially when working with legacy systems or preparing code for production deployment.