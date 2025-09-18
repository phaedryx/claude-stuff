# TOML-This Documentation Command

**Command**: `/toml-this <file_path>`

**Description**: Adds TOML-style documenting comments to Ruby files to improve code readability and understanding

## What This Command Does

This command analyzes Ruby files and adds structured TOML-style documentation comments that describe:

1. **File Purpose** - High-level description of what the file does
2. **Class/Module Documentation** - Purpose and responsibilities
3. **Method Documentation** - Parameters, return values, and behavior
4. **Dependencies** - Key gems, modules, or services used
5. **Configuration** - Important constants or configuration values
6. **Examples** - Usage examples where helpful

## Usage

```
/toml-this app/services/dam/asset_builder.rb
```

## Documentation Style

The command adds TOML-formatted comments using Ruby's `#` comment syntax:

```ruby
# [file]
# purpose = "Builds and processes DAM assets with variants and metadata"
# responsibility = "Asset creation, variant generation, metadata extraction"
# dependencies = ["ImageKit", "ActiveStorage", "Sidekiq"]

# [class.AssetBuilder]
# purpose = "Main service class for building DAM assets"
# input = "File upload data and company context"
# output = "Configured DAM asset with variants"

# [method.call]
# params = ["file_data: Hash", "company: Company", "options: Hash"]
# returns = "Dam::Asset"
# side_effects = ["Creates asset record", "Generates variants", "Extracts metadata"]
```

## Documentation Patterns

When `/toml-this` is executed with a file path, Claude will:

1. **Project Context Analysis**:
   - Read project standards from CLAUDE.md and .cursor/rules/ (if available)
   - Understand existing documentation patterns and conventions
   - Check for project-specific TOML documentation standards

2. **File Structure Analysis**:
   - Identify classes, modules, and key methods
   - Understand dependencies and imports  
   - Recognize patterns and responsibilities

2. **Add Structured Comments**:
   - File-level documentation at the top
   - Class/module documentation before definitions
   - Method documentation for public methods
   - Configuration documentation for constants

3. **Follow TOML Conventions**:
   - Use clear key-value pairs
   - Group related information in sections
   - Use arrays for lists (dependencies, params)
   - Use descriptive but concise values

## Documentation Sections

### File Level
- `purpose`: What the file accomplishes
- `responsibility`: Main area of concern
- `dependencies`: Key external dependencies
- `pattern`: Design pattern used (Service, Builder, etc.)

### Class/Module Level
- `purpose`: What the class does
- `input`: Expected input data
- `output`: What it produces
- `collaborators`: Other classes it works with

### Method Level
- `params`: Parameter descriptions with types
- `returns`: Return value description
- `side_effects`: External changes made
- `raises`: Exceptions that may be thrown

## Example Output

For a service class, the documentation might look like:

```ruby
# [file]
# purpose = "Processes user enrollment in courses with payment handling"
# responsibility = "Enrollment business logic and payment coordination"
# dependencies = ["PaymentService", "NotificationService", "Analytics"]
# pattern = "Service Object"

# [class.EnrollmentProcessor]
# purpose = "Handles complete enrollment workflow"
# input = "User, course, and payment information"
# output = "Enrollment record or error details"
# collaborators = ["PaymentService", "EmailService", "AuditLogger"]

class EnrollmentProcessor
  # [method.process]
  # params = ["user: User", "course: Course", "payment_data: Hash"]
  # returns = "Result object with enrollment or errors"
  # side_effects = ["Creates enrollment", "Processes payment", "Sends notifications"]
  # raises = ["PaymentError", "EnrollmentError"]
  def process(user, course, payment_data)
    # method implementation
  end
end
```

This structured approach makes code more maintainable and helps developers quickly understand file organization and method contracts.

## Error Handling

- **File not found**: Clear error message with suggested file paths
- **Non-Ruby files**: Inform user that command is designed for Ruby files
- **Parse errors**: Skip malformed sections, continue with rest of file
- **Existing documentation**: Merge with existing comments, don't overwrite

## Integration with Project Standards

- Preserves existing code structure and formatting
- Adds documentation without modifying functional code
- Follows Ruby commenting conventions
- Maintains compatibility with existing documentation tools