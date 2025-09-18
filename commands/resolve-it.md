# Resolve-It Command

**Command**: `/resolve-it`

**Description**: Automatically resolves merge conflicts across all files in the current branch using Claude's intelligent analysis.

## What This Command Does

When `/resolve-it` is executed, Claude will:

1. **Read Project Context**:
   - Check for conflict resolution guidelines in CLAUDE.md and .cursor/rules/
   - Understand project coding standards and patterns
   - Identify project-specific merge conflict preferences

2. **Detect merge conflicts** by checking:
   - `git status --porcelain` for conflict markers
   - `git diff --name-only --diff-filter=U` for unmerged files
   - `git ls-files --unmerged` for detailed conflict information

2. **Analyze each conflicted file** by:
   - Reading the file content with conflict markers
   - Understanding the code context and purpose
   - Identifying the nature of the conflict (structural, logical, stylistic)
   - Determining the intent of both branches

3. **Resolve conflicts intelligently** by:
   - Preserving functional code from both sides when possible
   - Following project coding standards from `.cursor/rules/`
   - Maintaining consistency with existing patterns
   - Ensuring no functionality is lost
   - Applying best practices for the specific file type

4. **Validate the resolution** by:
   - Checking syntax correctness
   - Ensuring imports/dependencies are intact
   - Verifying the resolution follows project conventions
   - Running any available linting or type checking

5. **Complete the merge** by:
   - Staging the resolved files with `git add`
   - Providing a summary of what was resolved
   - Suggesting next steps (tests, review, etc.)

## Conflict Resolution Strategy

### Code Conflicts
- **Additive changes**: Combine both additions when they don't conflict
- **Modifications**: Choose the more recent or more comprehensive change
- **Deletions**: Verify deletion intent and preserve necessary functionality
- **Imports/Dependencies**: Merge all necessary imports, remove duplicates

### Configuration Conflicts
- **Package dependencies**: Use the higher version when compatible
- **Settings**: Merge configurations, with explicit settings taking precedence
- **Environment variables**: Combine all necessary variables

### Documentation Conflicts
- **README/docs**: Merge content, preserving all useful information
- **Comments**: Combine explanations, remove redundant ones
- **AI documentation**: Follow `.cursor/rules/ai-documentation.mdc` standards

## Safety Measures

- **Backup**: Create a backup branch before resolving conflicts
- **Verification**: Ensure all tests pass after resolution
- **Review**: Flag complex conflicts that may need human review
- **Rollback**: Provide instructions to undo if resolution is incorrect

## Usage Example

```
/resolve-it
```

When merge conflicts exist after a failed merge, rebase, or cherry-pick, this command will automatically detect and resolve all conflicts across the entire branch.

## Output Format

```
= Detecting merge conflicts...
Found 3 conflicted files:
  - app/models/user.rb (method definition conflict)
  - package.json (dependency version conflict)
  - README.md (documentation conflict)

📝 Resolving app/models/user.rb...
  Conflict: Method 'validate_email' has different implementations
  Resolution: Combined both validation approaches
  Reasoning: Preserves existing validation while adding new email format check

=� Resolving package.json...
  Conflict: Different versions of 'react' dependency
  Resolution: Using higher version (18.2.0)
  Reasoning: Newer version includes security fixes and is backward compatible

=� Resolving README.md...
  Conflict: Different installation instructions
  Resolution: Merged both instruction sets
  Reasoning: Combined sections provide comprehensive setup guide

✅ All conflicts resolved!
📁 Staged 3 files for commit
🔧 Recommended next steps:
  1. Run tests: npm test / bundle exec rspec
  2. Verify functionality in development
  3. Create commit: git commit -m "Resolve merge conflicts"
```

## Error Handling

- **No conflicts found**: Inform user and exit gracefully
- **Complex conflicts**: Flag for manual review with detailed explanation
- **Syntax errors**: Fix common syntax issues during resolution
- **Unresolvable conflicts**: Provide guidance on manual resolution steps

## Integration with Project Standards

- Follows all rules in `.cursor/rules/` directory
- Maintains consistency with existing code patterns
- Preserves test coverage and functionality
- Ensures documentation stays current with code changes