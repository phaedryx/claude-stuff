# Review-It Command

**Command**: `/review-it`

**Description**: Conducts a comprehensive security and quality review of all changed files in the current branch using Claude's specialized pr-reviewer agent.

## What This Command Does

When `/review-it` is executed, Claude will:

1. **Smart Branch Handling**:
   - Check if current branch is prefixed with "tad/" (personal branches)
   - If "tad/" branch: automatically rebase with master and resolve conflicts before review
   - Use the same conflict resolution behavior as `/resolve-it` command
   - If conflicts exist after rebase, resolve them intelligently using project standards
   - Proceed with review only after clean rebase

2. **Branch Analysis**:
   - Scan all modified files in the current branch using `git diff --name-only HEAD^ HEAD`
   - Identify file types and apply appropriate analysis patterns
   - Understand each file's purpose and context within the project
   - Skip non-reviewable files (images, binaries, generated files)

3. **Security Review**:
   - Check for security vulnerabilities specific to the file type
   - Identify potential injection points or unsafe operations
   - Review authentication and authorization patterns
   - Check for data exposure or leakage risks
   - Validate input sanitization and output encoding

4. **Code Quality Assessment**:
   - Evaluate code structure, organization, and readability
   - Check adherence to project coding standards from `.cursor/rules/`
   - Identify potential performance issues and optimization opportunities
   - Review error handling patterns and edge case coverage
   - Assess maintainability and technical debt

5. **Best Practices Validation**:
   - Verify compliance with framework-specific best practices
   - Check for proper separation of concerns and SOLID principles
   - Review dependency management and version constraints
   - Validate testing patterns and coverage if applicable
   - Ensure consistent coding style and conventions

## Enhanced Workflow for Personal Branches

### For "tad/" Branches (Automatic Rebase + Review)

When executed on a branch prefixed with "tad/", the command performs:

1. **Pre-Review Rebase**:
   ```bash
   git rebase master
   ```

2. **Conflict Resolution** (if needed):
   - Detect merge conflicts using `git status --porcelain`
   - Apply intelligent conflict resolution using `/resolve-it` logic
   - Stage resolved files automatically
   - Continue rebase process

3. **Clean Review**:
   - Review only YOUR actual changes vs master (no noise from merged commits)
   - Focus on the diff between your branch and current master
   - Provide more accurate analysis of your specific contributions

### For Other Branches (Standard Review)

Standard behavior: review all modified files in current branch.

## Usage Examples

```
/review-it
```

**On "tad/" branch**: Rebases with master, resolves conflicts, then reviews your changes  
**On other branches**: Reviews all modified files in the current branch automatically

## Severity Levels and Decision Criteria

### ❌ **CRITICAL** - Must fix before deployment
- **Security vulnerabilities**: SQL injection, XSS, CSRF, authentication bypass
- **Data exposure**: Logging sensitive data, exposing secrets in responses
- **Breaking changes**: Code that will cause runtime errors or system failures
- **Performance killers**: N+1 queries, infinite loops, memory leaks

### ⚠️ **WARNING** - Should fix for better code quality  
- **Code smells**: Large methods, deep nesting, duplicated logic
- **Missing safeguards**: No input validation, missing error handling
- **Performance concerns**: Inefficient queries, unnecessary API calls
- **Maintainability**: Hard-coded values, unclear naming, missing tests

### ✅ **PASSING** - Meets standards
- **Security**: Proper input validation and sanitization
- **Quality**: Clear structure, appropriate abstractions, good naming
- **Performance**: Efficient algorithms and database usage
- **Standards**: Follows project conventions and best practices

## Review Areas

### Security Focus
- **Input validation**: SQL injection, XSS, parameter tampering protection
- **Authentication**: Session management, password handling, multi-factor auth
- **Authorization**: Role-based access, resource-level permissions, privilege escalation
- **Data handling**: PII protection, encryption at rest/transit, secure logging
- **External dependencies**: API key management, HTTPS usage, certificate validation

### Quality Focus
- **Code structure**: Single responsibility, appropriate abstractions, clear naming
- **Performance**: Database query optimization, caching strategies, algorithm efficiency
- **Error handling**: Graceful degradation, user-friendly messages, proper logging
- **Testing**: Unit test coverage, integration tests, edge case handling
- **Documentation**: Code comments for complex logic, README accuracy, API docs

### Standards Compliance
- **Project rules**: Adherence to `.cursor/rules/` standards and team conventions
- **Framework patterns**: Rails/React/etc. best practices and anti-patterns
- **Database patterns**: ActiveRecord best practices, migration safety, indexing
- **API design**: RESTful design, versioning, response consistency

## Output Format

### Enhanced Output for "tad/" Branches

```
🔄 Detected "tad/" branch - performing smart rebase and review...

📋 Step 1: Rebasing with master
git rebase master
Successfully rebased branch on latest master

📋 Step 2: Reviewing YOUR changes
Comparing tad/feature-branch with master...
Found 2 files with YOUR changes:
  - app/models/user.rb (your modifications)
  - app/services/new_feature.rb (your new file)

Skipped files from master updates:
  - app/services/analytics_processor.rb (from merged PR #123)
```

### Standard Output for Other Branches

```
🔍 Reviewing branch changes...
Found 3 modified files:
  - app/models/user.rb
  - app/controllers/api/payments_controller.rb  
  - app/services/payment_processor.rb

📋 Reviewing: app/services/payment_processor.rb

📋 File Overview:
  Type: Service Object
  Purpose: Processes payment transactions
  Dependencies: Stripe API, ActiveRecord
  Complexity: Medium

🛡️ Security Analysis:
  ✅ Input validation present for all user parameters
  ⚠️  API keys should use environment variables (line 23)
  ✅ Error handling prevents data leakage in responses
  ❌ Missing rate limiting for external API calls (CRITICAL)

📊 Code Quality:
  ✅ Clear method structure and naming conventions
  ✅ Proper error handling with specific exceptions
  ⚠️  Consider extracting validation logic (lines 45-67) to concern
  ✅ Good separation of concerns between payment and notification logic

🎯 Recommendations:
  1. Move API keys to environment variables
  2. Implement rate limiting for Stripe API calls
  3. Add logging for audit trail
  4. Consider adding circuit breaker pattern
  5. Extract validation into separate concern

📝 Next Steps:
  - Address security concerns before deployment
  - Consider adding integration tests
  - Update documentation with new patterns
```

## File Type Specific Reviews

### Ruby Files (`.rb`)
- **Models**: ActiveRecord validations, associations, scopes, callbacks, N+1 queries
- **Services**: Single responsibility, error handling, transaction management
- **Controllers**: Strong parameters, authentication, authorization, response formats
- **Jobs**: Retry logic, error handling, performance optimization
- **Migrations**: Reversibility, indexing, data safety

### JavaScript/TypeScript Files (`.js`, `.ts`, `.jsx`, `.tsx`)
- **Security**: XSS prevention, input sanitization, CSRF protection
- **Performance**: Bundle size, code splitting, memory leaks, infinite re-renders
- **React patterns**: Hook usage, component structure, state management
- **TypeScript**: Type safety, interface definitions, generic usage

### Configuration Files
- **Gemfile/package.json**: Dependency versions, security vulnerabilities, licensing
- **Environment configs**: Secret management, environment-specific settings
- **Database configs**: Connection pooling, timeout settings, SSL requirements
- **Deployment configs**: Resource limits, health checks, scaling policies

### Template Files (`.erb`, `.haml`, `.jsx`)
- **Security**: Output encoding, CSRF tokens, content security policy
- **Performance**: N+1 queries from views, excessive DOM manipulation
- **Accessibility**: ARIA labels, semantic HTML, keyboard navigation
- **SEO**: Meta tags, structured data, semantic markup

### Database Files (`.sql`, migrations)
- **Safety**: Data migration safety, rollback procedures, backup considerations
- **Performance**: Indexing strategies, query optimization, constraint efficiency
- **Schema**: Referential integrity, normalization, data types

## Error Handling and Edge Cases

### No Modified Files
```
🔍 Reviewing branch changes...
No modified files found in current branch.
💡 Tip: Make some changes and commit them, or use git diff to check unstaged changes.
```

### Non-Reviewable Files Only
```
🔍 Reviewing branch changes...
Found 3 modified files (2 images, 1 binary) - no reviewable code files.
ℹ️  Skipped: logo.png, app.exe, data.db
```

### Large Files Warning
```
⚠️  Large file detected: app/models/large_model.rb (1,200 lines)
This file may need to be reviewed in sections. Consider refactoring into smaller modules.
```

### Missing Project Standards
```
⚠️  No .cursor/rules/ directory found
Using general best practices instead of project-specific standards.
Consider adding project-specific coding rules in .cursor/rules/
```

## Integration with Project Standards

- **Primary source**: Rules and patterns defined in `.cursor/rules/` directory
- **Fallback**: Framework-specific best practices (Rails, React, etc.)
- **Context awareness**: Considers existing project patterns and conventions
- **Actionable output**: Provides specific line numbers and concrete fix suggestions
- **Consistency**: Maintains review quality across different file types and team members

## Performance Considerations

- **File size limits**: Files over 1,000 lines receive focused review on changed sections
- **Binary file detection**: Automatically skips images, executables, and data files
- **Incremental analysis**: Reviews only files changed in current branch, not entire codebase
- **Parallel processing**: When multiple files exist, reviews are conducted efficiently