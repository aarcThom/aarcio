# GIT

## Git Configuration

### Gotchas

#### Tracking Folder Case Changes

Git, by default, *does not track case changes.* In a static website this will lead to 404 errors.

To fix this, navigate to the projects folder and run:

```bash
git config core.ignorecase false
```
