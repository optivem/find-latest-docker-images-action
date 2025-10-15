# Contributing to Inspect Docker Image Action

Thank you for your interest in contributing to this project! Here's how you can help make this GitHub Action better.

## How to Contribute

### Reporting Issues

- **Search existing issues** first to avoid duplicates
- **Use issue templates** when available
- **Provide clear reproduction steps** for bugs
- **Include relevant logs** and error messages

### Suggesting Features

- **Check existing feature requests** to avoid duplicates
- **Explain the use case** and why the feature would be valuable
- **Provide implementation suggestions** if you have them

### Contributing Code

1. **Fork the repository**
2. **Create a feature branch** from `main`
3. **Make your changes**
4. **Test your changes** thoroughly
5. **Submit a pull request**

### Development Setup

1. Clone your fork:
   ```bash
   git clone https://github.com/YOUR-USERNAME/find-latest-docker-images-action.git
   cd find-latest-docker-images-action
   ```

2. Make sure you have PowerShell installed for testing the script locally

3. Test the action locally by running the PowerShell script:
   ```powershell
   ./action.ps1 -RepoOwner "test" -RepoName "test" -ImageName "test" -Version "latest"
   ```

### Testing

- Test your changes with different Docker images
- Ensure the action works on different operating systems (Ubuntu, Windows, macOS)
- Verify that error handling works correctly
- Check that the action follows GitHub Actions best practices

### Code Style

- Use clear, descriptive variable names
- Add comments for complex logic
- Follow PowerShell best practices
- Keep the action.yml schema valid

### Pull Request Guidelines

- **Title**: Use a clear, descriptive title
- **Description**: Explain what changes you made and why
- **Testing**: Describe how you tested your changes
- **Breaking Changes**: Clearly note any breaking changes

### Release Process

This project follows semantic versioning:
- **Patch** (1.0.1): Bug fixes
- **Minor** (1.1.0): New features (backward compatible)
- **Major** (2.0.0): Breaking changes

## Code of Conduct

Please be respectful and professional in all interactions. We want this to be a welcoming community for everyone.

## Questions?

If you have questions, feel free to:
- Open an issue for discussion
- Check existing documentation
- Look at the examples in the README

Thank you for contributing! 🎉