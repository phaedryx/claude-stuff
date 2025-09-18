# Document-It Command

**Command**: `/document-it`

**Description**: Automatically updates *.ai.md files to reflect changes in the current git branch.

## What This Command Does

When `/document-it` is executed, Claude will:

1. **Find all new and modified files** in the current git branch by checking:
   - Files modified in the latest commit (`git diff --name-only HEAD^ HEAD`)
   - Currently staged files (`git diff --cached --name-only`)
   - Currently unstaged files (`git diff --name-only`)

2. **Determine corresponding *.ai.md files** by:
   - Taking each modified file's directory and base name
   - Looking for `[basename].ai.md` in the same directory
   - Following the naming convention from `.cursor/rules/ai-documentation.mdc`

3. **Update *.ai.md files** to reflect code changes by:
   - Reading the current code file content
   - Reading the existing *.ai.md file
   - Updating the documentation to match current implementation
   - Maintaining the required format and structure per ai-documentation.mdc rules

4. **Ensure compliance** with documentation standards:
   - Must follow the format requirements in `.cursor/rules/ai-documentation.mdc`
   - Must include clear title, description, rule name sections
   - Must include practical examples
   - Must maintain consistency with existing patterns

## Usage Example

```
/document-it
```

## When To Use This Command

This command is particularly useful for:
- **Pre-commit documentation**: Ensure all changes have proper AI documentation
- **Feature branch completion**: Update docs before merging
- **Documentation maintenance**: Keep *.ai.md files synchronized with code
- **Onboarding preparation**: Ensure new code is properly documented

## Process Flow

1. **Discovery Phase**:
   - Scans git status for all changed files
   - Identifies which files have corresponding *.ai.md documentation
   - Reports findings to user

2. **Analysis Phase**:
   - Reads each modified code file to understand changes
   - Compares with existing *.ai.md documentation
   - Identifies what documentation needs updating

3. **Update Phase**:
   - Updates *.ai.md files to reflect code changes
   - Ensures consistency with project documentation standards
   - Maintains proper TOML-style formatting and structure

4. **Validation Phase**:
   - Verifies updated documentation follows `.cursor/rules/ai-documentation.mdc`
   - Checks for completeness and accuracy
   - Reports summary of changes made

## Output Format

```
🔍 Scanning for modified files...
Found 3 modified files:
  - app/models/user.rb
  - app/services/notification_service.rb
  - app/controllers/api/users_controller.rb

📋 Checking for corresponding *.ai.md files...
Found documentation for:
  ✅ app/models/user.ai.md (corresponds to user.rb)
  ✅ app/services/notification_service.ai.md (corresponds to notification_service.rb)
  ❌ No documentation found for users_controller.rb

📝 Updating documentation...
  Updated: app/models/user.ai.md
    - Added new validation rules
    - Updated method signatures
    - Added new callback documentation
  
  Updated: app/services/notification_service.ai.md
    - Updated method parameters
    - Added new error handling documentation
    - Updated example usage

✅ Documentation update complete!
📁 2 files updated, 1 file needs new documentation
```

## File Creation Suggestions

If no corresponding *.ai.md files exist, the command will suggest where they should be created:

```
Consider creating *.ai.md files for your modified code files:
  Could create: app/controllers/api/users_controller.ai.md
  Could create: app/models/user.ai.md
  Could create: app/services/notification_service.ai.md
```

## Error Handling and Edge Cases

### No Modified Files
```
🔍 Scanning for modified files...
No modified files found in current branch.
💡 Tip: Make some changes and commit them, or check git status for unstaged files.
```

### Missing .cursor/rules/ai-documentation.mdc
```
⚠️  No .cursor/rules/ai-documentation.mdc found
Using general documentation patterns instead of project-specific format.
Consider adding ai-documentation.mdc to define project standards.
```

### Mixed File Types
```
🔍 Scanning for modified files...
Found 5 modified files:
  - app/models/user.rb (Ruby - will check for documentation)
  - package.json (Config - skipping documentation check)
  - image.png (Binary - skipping documentation check)
  
ℹ️  Only checking Ruby and other code files for *.ai.md documentation.
```

## Integration with Project Standards

- **Primary source**: Rules and format from `.cursor/rules/ai-documentation.mdc`
- **Fallback**: General *.ai.md documentation patterns when project rules missing
- **Consistency**: Maintains existing *.ai.md file patterns and structure
- **Preservation**: Keeps documentation formatting and examples intact
- **Synchronization**: Ensures all code changes have corresponding documentation updates