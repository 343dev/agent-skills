# Sources And Attribution

This skill is an implementation-oriented adaptation and summary of the Command Line Interface Guidelines.

## Primary Sources

- Official guide: https://clig.dev/
- Source repository: https://github.com/cli-guidelines/cli-guidelines
- Canonical content: https://github.com/cli-guidelines/cli-guidelines/blob/main/content/_index.md
- Repository README: https://github.com/cli-guidelines/cli-guidelines/blob/main/README.md

The official repository states that the guide content lives in `content/_index.md`. Consult that file when exact wording or newly updated guidance matters.

## Authors

The guide credits Aanand Prasad, Ben Firshman, Carl Tashian, and Eva Parish, with design by Mark Hurrell and additional reviewers and contributors listed on the official site.

## License

The guide is licensed under the Creative Commons Attribution-ShareAlike 4.0 International License:

https://creativecommons.org/licenses/by-sa/4.0/

This skill paraphrases and reorganizes the guide for agent use. Preserve this attribution and compatible licensing obligations when redistributing adapted portions.

## Stable Section Links

- Philosophy: https://clig.dev/#philosophy
- Basics: https://clig.dev/#the-basics
- Help: https://clig.dev/#help
- Documentation: https://clig.dev/#documentation
- Output: https://clig.dev/#output
- Errors: https://clig.dev/#errors
- Arguments and flags: https://clig.dev/#arguments-and-flags
- Interactivity: https://clig.dev/#interactivity
- Subcommands: https://clig.dev/#subcommands
- Robustness: https://clig.dev/#robustness-guidelines
- Future-proofing: https://clig.dev/#future-proofing
- Signals: https://clig.dev/#signals
- Configuration: https://clig.dev/#configuration
- Environment variables: https://clig.dev/#environment-variables
- Naming: https://clig.dev/#naming
- Distribution: https://clig.dev/#distribution
- Analytics: https://clig.dev/#analytics

## Important Precision Notes

- The four rules in "The Basics" are the guide's explicitly essential baseline: parser library, correct exit codes, primary output on `stdout`, and messaging on `stderr`.
- `NO_COLOR` disables color when set to a non-empty value under the current official wording.
- TTY and color behavior should be assessed per stream.
- Appending `-h` or `--help` should remain useful even when other input is invalid.
- `--no-input` disables interaction; it does not authorize destructive actions.
- Passing a secret through shell substitution into a flag retains the exposure risks of a secret flag.
- Human-readable output may evolve; explicitly supported machine output is the compatibility boundary.
