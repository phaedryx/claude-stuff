# Spec-It Command

**Command**: `/spec-it`

**Description**: Automatically creates and runs RSpec specs for all new or modified files in the current branch, iterating until all tests pass.

## What This Command Does

When `/spec-it` is executed, Claude will:

1. **Find all new or modified files** in the current branch by checking:
   - Files modified in the latest commit (`git diff --name-only HEAD^ HEAD`)
   - Currently staged files (`git diff --cached --name-only`)
   - Currently unstaged files (`git diff --name-only`)
   - Filter for Ruby files that need spec coverage:
     - `app/models/*.rb` → `spec/models/*_spec.rb`
     - `app/controllers/*.rb` → `spec/controllers/*_spec.rb`
     - `app/services/*.rb` → `spec/services/*_spec.rb`
     - `app/lib/*.rb` → `spec/lib/*_spec.rb`

2. **Write RSpec specs to cover the changes** by:
   - Analyzing each modified file's code structure
   - Creating comprehensive specs for new methods and classes
   - Updating existing specs to cover modified functionality
   - Following `.cursor/rules/rspec-best-practices.mdc` standards
   - Using explicit test data creation patterns

3. **Run the augmented spec files** by:
   - Executing RSpec on the modified spec files
   - Capturing test output and failures
   - Identifying specific test failures and their causes

4. **Refactor until all specs pass** by:
   - Analyzing test failures and their root causes
   - Refactoring code to make tests pass
   - Re-running specs after each change
   - Iterating until all new and modified specs are green

## Usage Example

```
/spec-it
```

## Process Flow

### 1. Discovery Phase
```
🔍 Scanning for modified files...
Found 4 modified Ruby files:
  ✅ app/models/user.rb (has spec)
  ✅ app/services/notification_service.rb (has spec)
  ❌ app/controllers/api/users_controller.rb (missing spec)
  ❌ app/lib/email_validator.rb (missing spec)
```

### 2. Spec Creation Phase
```
📝 Creating specs for modified code...

Writing: spec/models/user_spec.rb
  - Added tests for new validation methods
  - Updated existing tests for modified behavior
  - Added edge case coverage

Writing: spec/services/notification_service_spec.rb
  - Added tests for new error handling
  - Updated method signature tests
  - Added integration test scenarios

Creating: spec/controllers/api/users_controller_spec.rb
  - Created comprehensive controller tests
  - Added authentication/authorization tests
  - Added response format validation

Creating: spec/lib/email_validator_spec.rb
  - Created unit tests for validation logic
  - Added edge case and error condition tests
  - Added performance consideration tests
```

### 3. Test Execution Phase
```
🧪 Running augmented specs...
bundle exec rspec spec/models/user_spec.rb spec/services/notification_service_spec.rb spec/controllers/api/users_controller_spec.rb spec/lib/email_validator_spec.rb

Results:
  ❌ 3 failures, 12 passing
  
Failures:
  1. User validation fails for new email format
  2. NotificationService missing error handling for nil user
  3. EmailValidator doesn't handle international domains
```

### 4. Refactoring Phase
```
🔧 Refactoring to fix test failures...

Fixing: app/models/user.rb
  - Updated email validation regex pattern
  - Added nil check in validation method

Fixing: app/services/notification_service.rb
  - Added nil user handling with early return
  - Improved error messaging

Fixing: app/lib/email_validator.rb
  - Added international domain support
  - Improved Unicode handling

🧪 Re-running specs...
bundle exec rspec spec/models/user_spec.rb spec/services/notification_service_spec.rb spec/controllers/api/users_controller_spec.rb spec/lib/email_validator_spec.rb

✅ All tests passing! (15 examples, 0 failures)
```

## RSpec Best Practices Integration

Following `.cursor/rules/rspec-best-practices.mdc`:

### Test Structure
- **Explicit test data creation**: Use fixtures → application code → factories
- **No implicit subjects**: Always use explicit class names
- **Observable behavior only**: Test outcomes, not implementation details
- **Clear test organization**: Group related tests logically

### Avoided Patterns
- No `let`, `before`, `shared_context`, `subject`, `described_class`
- No testing implementation details
- No overly complex test setups

### Example Spec Style
```ruby
# spec/services/notification_service_spec.rb
RSpec.describe NotificationService do
  describe "#send_welcome_email" do
    it "sends email to valid user" do
      user = User.create!(email: "test@example.com", name: "Test User")
      service = NotificationService.new
      
      result = service.send_welcome_email(user)
      
      expect(result.success?).to be true
      expect(ActionMailer::Base.deliveries.last.to).to include("test@example.com")
    end

    it "handles nil user gracefully" do
      service = NotificationService.new
      
      result = service.send_welcome_email(nil)
      
      expect(result.success?).to be false
      expect(result.error).to eq("User cannot be nil")
    end
  end
end
```

## File Type Coverage

### Models (`app/models/`)
- Validation specs
- Association specs
- Instance method specs
- Class method specs
- Callback specs

### Controllers (`app/controllers/`)
- Action specs with various scenarios
- Authentication/authorization specs
- Parameter handling specs
- Response format specs
- Error handling specs

### Services (`app/services/`)
- Public method specs
- Error condition specs
- Integration specs with dependencies
- Business logic validation

### Libraries (`app/lib/`, `lib/`)
- Unit tests for all public methods
- Edge case handling
- Error condition testing
- Performance considerations

## Iteration Strategy

The command will iterate through the refactoring cycle:

1. **Run specs** and capture failures
2. **Analyze failures** to understand root causes
3. **Make minimal changes** to fix specific issues
4. **Re-run specs** to verify fixes
5. **Repeat** until all specs pass

Maximum of 5 refactoring iterations to prevent infinite loops.

## Integration with Project Standards

- Follows all rules in `.cursor/rules/rspec-best-practices.mdc`
- Maintains consistency with existing spec patterns
- Uses project-specific testing utilities and helpers
- Ensures comprehensive coverage of modified functionality
- Validates that changes don't break existing functionality