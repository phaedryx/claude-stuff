# N+1-It Command

**Command**: `/nplus1-it`

**Description**: Analyzes all modified Ruby on Rails files in the current branch for potential N+1 query patterns and violations

## What This Command Does

This command systematically searches through all modified Rails files in the current branch to identify N+1 query anti-patterns, focusing on:

1. **Controller Actions** - Missing `includes/preload/eager_load` for associations used in views or serialization
2. **Instance Methods** - Model methods that query associations when called on collections
3. **View Templates** - Association access in loops without proper eager loading
4. **Service Objects** - Business logic that iterates over collections accessing associations
5. **Background Jobs** - Batch processing without proper includes
6. **API/JSON Building** - Response building that accesses associations in loops
7. **Blueprint/Serializer Usage** - Missing eager loading for serialized associations

## Analysis Pattern

When `/nplus1-it` is executed, Claude will:

1. **Scan Modified Files for N+1 Patterns**:
   - Find all modified .rb files in the current branch
   - Focus analysis on recently changed code
   - Search for common N+1 patterns:
   - `each` loops accessing associations: `collection.each { |item| item.association }`
   - Instance methods called on collections that query associations
   - Controller actions serving JSON/views without includes
   - Blueprint/serializer usage without proper eager loading

2. **Check Against Rails Best Practices**:
   - Verify controller actions include required associations
   - Identify instance methods that cause N+1 when called on collections
   - Find view templates accessing associations in loops
   - Detect missing counter caches for simple counts

3. **Provide Specific Fixes**:
   - Suggest specific `includes`, `preload`, or `eager_load` statements
   - Recommend counter caches for count operations
   - Propose query object patterns for complex scenarios
   - Identify opportunities for database-level aggregations

## Detection Criteria

Based on the Rails best practices rules, I will flag:

- **Controller actions** without includes that serve views/JSON accessing associations
- **Instance methods** that query associations (especially counts/sums) when potentially called on collections
- **View loops** accessing associations without eager loading in controller
- **Service objects** iterating collections and accessing associations
- **Background jobs** processing records without proper includes
- **CSV/report generation** accessing associations in loops
- **API responses** building nested data without eager loading

## Output Format

**Severity Classification:**
- **🚨 CRITICAL**: Definite N+1 queries that will cause performance issues in production
- **⚠️ WARNING**: Potential N+1 patterns that may cause issues under load
- **✅ CLEAN**: No N+1 patterns detected

For each N+1 pattern found, the analysis provides:

1. **File location**: `file_path:line_number` 
2. **Pattern type**: Controller, Instance Method, View, Service, etc.
3. **Specific issue**: What association access is causing N+1
4. **Recommended fix**: Exact code changes needed with specific Rails methods
5. **Severity**: Risk level and potential impact

## Example Usage

```
/nplus1-it
```

Will scan all modified files in the current branch and report potential N+1 query issues with specific fixes.

## Branch-Focused Analysis

This command is particularly useful for:
- **Pre-commit reviews**: Check your changes before committing
- **Feature branch validation**: Ensure new code doesn't introduce N+1 queries
- **Performance regression prevention**: Catch N+1 issues in changed files
- **Focused optimization**: Only analyze what you've modified

## Process Flow

1. **Discovery**: Scan git for modified .rb files
2. **Analysis**: Check each file for N+1 patterns
3. **Context**: Understand how changes might affect query patterns
4. **Reporting**: Provide specific fixes for identified issues

## Example Output Format

```
🔍 Scanning modified files in branch...
Found 3 modified Ruby files:
  - app/controllers/users_controller.rb
  - app/models/user.rb  
  - app/services/user_report_service.rb

📊 Analyzing N+1 patterns...

❌ CRITICAL: app/controllers/users_controller.rb:15
  Pattern: Controller action without includes
  Issue: @users.each { |user| user.posts.count } in view
  Fix: Add .includes(:posts) to User.all query
  
⚠️  WARNING: app/services/user_report_service.rb:23
  Pattern: Instance method in loop
  Issue: users.map(&:total_score) causes N+1 queries
  Fix: Add counter_cache :total_score to User model

✅ app/models/user.rb - No N+1 patterns detected

Summary: 1 critical, 1 warning, 0 clean files
```