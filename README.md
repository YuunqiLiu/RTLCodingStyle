# RTL Coding Style Guide

Best practices for writing clean, maintainable Verilog/SystemVerilog RTL code.

## Documentation

The documentation is available in both English and Chinese:

- **Landing page**: [https://yuunqiliu.github.io/RTLCodingStyle/](https://yuunqiliu.github.io/RTLCodingStyle/)
- **English**: [https://yuunqiliu.github.io/RTLCodingStyle/en/](https://yuunqiliu.github.io/RTLCodingStyle/en/)
- **中文**: [https://yuunqiliu.github.io/RTLCodingStyle/zh/](https://yuunqiliu.github.io/RTLCodingStyle/zh/)

## Build Locally

```bash
pip install sphinx sphinx_rtd_theme

# English
sphinx-build source build/en

# Chinese
sphinx-build source_zh build/zh
```

## AI Agent Skill

The `skill/SKILL.md` file contains a self-contained AI agent skill that encapsulates all RTL coding style rules. It can be used with Claude, Copilot, or other AI coding assistants to automatically apply these rules during code generation and review.

The skill is published as a release artifact on every version tag.

## CI/CD

- **Deploy Docs** (`deploy.yml`): Builds and deploys bilingual docs to GitHub Pages on every push to `main`.
- **CI & Release** (`ci-release.yml`): Validates doc builds on every push/PR. On version tags (`v*`), creates a GitHub Release with:
  - Packaged docs (`.tar.gz`)
  - SKILL.md (standalone + packaged)

## Release

To create a new release:

```bash
git tag v1.1
git push origin v1.1
```


