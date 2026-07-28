# Contributing

Thanks for your interest in this project.

## All code changes happen through pull requests (PRs)

Pull requests are the best way to propose changes to the codebase (using the standard [GitHub Flow](https://guides.github.com/introduction/flow/index.html)).

Some PR guidance:

- Please keep PRs small and focused on a single feature or change, with discrete commits. Use multiple PRs if need be.
- If you're thinking of adding a feature via a PR please create an issue first where it can be discussed.

High level steps:

1. Create a branch from `main`.
2. If you've changed APIs, update the documentation.
3. Make sure your code lints (run `make lint`).
4. Ensure the test suite passes (run `make test` against a running instance).
5. Open the pull request.

## Any contributions you make will be under the MIT Software License

In short, when you submit code changes, your submissions are understood to be under the same [MIT License](http://choosealicense.com/licenses/mit/) that covers the project.

## Report bugs using GitHub's issues

This project uses GitHub issues to track bugs. Report a bug by opening a new issue.

## Write bug reports with detail, background, and sample code

**Great Bug Reports** tend to have:

- A quick summary and/or background
- Steps to reproduce
  - Be specific!
  - Give sample code if you can. Even if it's a snippet
- What you expected would happen
- What actually happens
- Notes (possibly including why you think this might be happening, or stuff you tried that didn't work)

## Use a consistent coding style

Run `make lint` to check formatting & linting issues, and `cd src && npm run lint-fix` to fix them automatically. A [Prettier](https://prettier.io/) configuration file is included.
