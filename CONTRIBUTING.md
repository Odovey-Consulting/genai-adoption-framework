# Contributing to the GenAI Adoption Framework

Welcome, and thank you for your interest in contributing to the GenAI Adoption Framework. This project aims to provide organizations with a vendor-agnostic, principled guide for adopting generative AI at enterprise scale. Contributions from security architects, platform engineers, governance professionals, and practitioners of all backgrounds are encouraged.

## How to Contribute

There are several ways to get involved:

- **Open an Issue.** If you find an error, a gap in coverage, or have a suggestion for improvement, open a GitHub issue. Please include a clear title, a description of the problem or proposal, and any relevant context such as the layer or section of the framework affected.
- **Submit a Pull Request.** If you would like to contribute content directly, fork the repository, create a feature branch, make your changes, and open a pull request against the `main` branch. Keep pull requests focused on a single concern. Large, sweeping changes should be discussed in an issue first.
- **Join the Discussion.** Use GitHub Discussions for open-ended questions, ideas for new sections, or feedback on the framework's direction. This is the preferred venue for conversations that do not map neatly to a bug report or a specific change.

## Content Guidelines

All contributions must adhere to the following principles:

- **Vendor-agnostic.** Do not reference specific commercial products, cloud providers, or proprietary platforms by name. Use generic terms such as "API gateway," "identity provider," or "container orchestration platform." The framework must remain applicable regardless of an organization's technology stack.
- **No product names.** Avoid brand names, trademarked tool names, and vendor-specific terminology. If a concept is commonly associated with a single vendor, describe the capability abstractly rather than naming the tool.
- **Theoretical and principled.** Focus on principles, patterns, and architectural reasoning rather than implementation tutorials or step-by-step configuration guides. The goal is to inform decision-making, not to prescribe a specific toolchain.
- **Grounded in practice.** While the framework is principled, contributions should reflect real-world applicability. Abstract guidance should be actionable enough for an architect or engineering lead to translate into concrete decisions.

## Writing Style

- Write in a professional, concise, and direct tone.
- Use active voice and short paragraphs.
- Prefer bullet points and tables over long prose when presenting structured information.
- Every section should answer the question: "What should the reader do with this information?"
- Define acronyms on first use within each document.

## File Structure Conventions

- Place framework content under the `docs/` directory, organized by layer or topic.
- Use lowercase filenames with hyphens as separators (e.g., `governance-layer.md`).
- Store diagrams and visual assets in the `assets/` directory.
- Mermaid diagram source files should use the `.mmd` extension.
- Keep one logical topic per file. If a file exceeds roughly 500 lines, consider splitting it.

## Pull Request Process

1. Ensure your branch is up to date with `main` before submitting.
2. Provide a clear PR title and description summarizing the change and its motivation.
3. Reference any related issues using GitHub's `#issue-number` syntax.
4. At least one maintainer review is required before merging.
5. All discussions and requested changes must be resolved before a PR is merged.
6. Squash commits into a single, well-described commit when merging.

## Code of Conduct

This project follows a standard open-source code of conduct. All participants are expected to treat each other with respect, engage constructively, and foster an inclusive environment. Harassment, discrimination, and disruptive behavior will not be tolerated. Violations may be reported to the project maintainers.

## License

By contributing to this repository, you agree that your contributions will be licensed under the Apache License 2.0. See the [LICENSE](LICENSE) file for the full license text.
