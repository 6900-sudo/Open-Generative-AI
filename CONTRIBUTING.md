# Contributing Guidelines

## Branching Strategy

This project uses a feature branch workflow to keep the main branch stable.

### Branch Types

1. **main** - Production-ready code (protected)
2. **feature/** - New features (`feature/add-auth`, `feature/user-dashboard`)
3. **bugfix/** - Bug fixes (`bugfix/login-error`, `bugfix/memory-leak`)
4. **docs/** - Documentation updates (`docs/api-guide`, `docs/setup`)
5. **chore/** - Maintenance tasks (`chore/deps-update`, `chore/cleanup`)

### Workflow

1. Create a feature branch from `main`:
   ```bash
   git checkout main
   git pull origin main
   git checkout -b feature/your-feature-name
   ```

2. Make your changes and commit:
   ```bash
   git add .
   git commit -m "feat: description of changes"
   ```

3. Push to your branch:
   ```bash
   git push -u origin feature/your-feature-name
   ```

4. Create a Pull Request on GitHub
   - Link related issues
   - Describe changes clearly
   - Request review from team members

5. After approval, merge via GitHub UI

### Commit Message Format

Follow conventional commits:

```
type(scope): subject

body

footer
```

Types: feat, fix, docs, style, refactor, perf, test, chore
Example: `feat(auth): add login validation`

### Do NOT

❌ Push directly to `main`
❌ Force push to shared branches
❌ Commit large files or secrets
❌ Merge without review

