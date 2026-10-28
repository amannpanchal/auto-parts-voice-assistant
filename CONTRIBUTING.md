# Contributing Guidelines

Thank you for your interest in contributing to LiveKit AI Car Call Centre! This document outlines the process and standards for contributions.

## Code of Conduct

This project follows a standard code of conduct. Be respectful, inclusive, and constructive in all interactions.

## How to Contribute

### 1. Reporting Issues

Before creating an issue, search existing issues to avoid duplicates. When reporting:

- **Bug Reports**: Include steps to reproduce, expected vs actual behavior, environment details
- **Feature Requests**: Describe the use case, proposed solution, and alternatives considered
- **Documentation**: Point out unclear, missing, or incorrect documentation

### 2. Pull Request Process

1. **Fork** the repository
2. **Create a branch**: `git checkout -b feature/your-feature-name` or `fix/your-fix-name`
3. **Make changes** following the coding standards below
4. **Test your changes** locally
5. **Commit** with conventional commit messages
6. **Push** to your fork
7. **Open a Pull Request** against the main branch

### 3. Commit Message Convention

Use [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

Types:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style (formatting, no logic changes)
- `refactor`: Code restructuring without behavior changes
- `test`: Adding or updating tests
- `chore`: Maintenance tasks

Examples:
```
feat: add dark mode toggle to header
fix: resolve token expiry handling in LiveKitModal
docs: update API.md with new endpoints
style: format CSS with consistent spacing
```

## Coding Standards

### Frontend (React + CSS)

#### Component Structure
```jsx
// Component.jsx
import { useState, useEffect, useCallback } from 'react';
import './Component.css'; // Co-located styles

const Component = ({ prop1, prop2 }) => {
  // State hooks first
  const [state, setState] = useState(initialValue);
  
  // Effects
  useEffect(() => {
    // side effects
  }, [dependencies]);
  
  // Callbacks
  const handleClick = useCallback(() => {
    // handler
  }, [dependencies]);
  
  // Render
  return (
    <div className="component">
      {/* JSX */}
    </div>
  );
};

export default Component;
```

#### CSS Guidelines
- Use CSS custom properties (variables) from design system
- Follow BEM-ish naming: `.component__element--modifier`
- Mobile-first responsive design
- Support dark/light mode via `prefers-color-scheme` or class
- Prefer `rem` for spacing/typography, `px` for borders

#### Accessibility
- Semantic HTML elements
- ARIA labels for interactive elements
- Focus indicators on all focusable elements
- Color contrast ratios (WCAG AA minimum)
- Keyboard navigation support

### Backend (Python)

#### Code Style
- Follow PEP 8 (enforced by ruff/black if configured)
- Type hints for function signatures
- Docstrings for public functions/classes
- Max line length: 100 characters

#### Async Patterns
```python
# Good: async for I/O operations
@app.route("/api/endpoint")
async def endpoint():
    result = await async_operation()
    return jsonify(result)

# Avoid: blocking calls in async functions
# Bad: time.sleep() in async function
```

#### Error Handling
- Use specific exception types
- Return appropriate HTTP status codes
- Log errors with context (but not secrets)

### Design System Usage

Always use design system tokens instead of hardcoded values:

```css
/* Good */
.button {
  background-color: var(--color-primary);
  padding: var(--spacing-md);
  border-radius: var(--radius-md);
}

/* Bad */
.button {
  background-color: #4F46E5;
  padding: 16px;
  border-radius: 8px;
}
```

## Testing

### Frontend
```bash
cd frontend
npm run lint      # ESLint
npm run build     # Type-check + build
# Add: npm test when test framework is added
```

### Backend
```bash
cd backend
# Add pytest when tests exist
python -m pytest
```

### Manual Testing Checklist

Before submitting a PR, verify:

- [ ] Frontend loads without console errors
- [ ] Support modal opens and connects to LiveKit
- [ ] Voice assistant renders correctly
- [ ] Responsive layout works at 320px, 768px, 1024px, 1440px
- [ ] Dark mode (if implemented) works correctly
- [ ] API endpoints return expected responses
- [ ] No new linting errors

## Pull Request Review

PRs will be reviewed for:

1. **Functionality** - Does it work as intended?
2. **Code Quality** - Follows conventions, no obvious bugs
3. **Design Consistency** - Matches design system
4. **Documentation** - Updated if needed
5. **Tests** - Added or updated (when test infrastructure exists)

## Release Process

1. Version bump in `package.json` (frontend) and appropriate backend files
2. Update `CHANGELOG.md` with release notes
3. Create release tag: `git tag v1.0.0`
4. Deploy to staging, then production

## Getting Help

- Check existing documentation in `docs/`
- Review `DEVELOPMENT.md` for setup issues
- Open a discussion for design questions
- Tag maintainers in PR for urgent reviews

## License

By contributing, you agree that your contributions will be licensed under the project's license (to be determined - currently no license file exists).

---

**Thank you for contributing!** 🚗💬