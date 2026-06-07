# Repository Guidelines

## Project Structure & Module Organization

This repository publishes Shadowrocket configuration files and rule lists. `source/` contains curated input lists and shared config text, especially `source/General.txt`. `rules/` contains generated Shadowrocket rule sets such as `domains_community.list`, `ips_refilter.list`, and `voice_ports.list`. `conf/` contains generated user-facing configs, including `sr_ru_basic.conf`, `sr_ru_geo.conf`, and `sr_nonru_basic.conf`. `modules/` stores Shadowrocket modules, and `scripts/` stores rewrite scripts used by modules. Automation is defined in `.github/workflows/run.yml`.

## Build, Test, and Development Commands

There is no local package build. The main generation path is GitHub Actions:

- `gh workflow run run.yml` triggers the generator on GitHub.
- `git log --oneline -n 10` checks recent automated and manual commit style.
- `rg "RULE-SET|DOMAIN-SUFFIX|IP-CIDR" rules conf` quickly inspects generated routing rules.

When changing generator logic, prefer editing `.github/workflows/run.yml` and then triggering the workflow rather than hand-editing generated files.

## Coding Style & Naming Conventions

Keep Shadowrocket syntax exact: use uppercase rule types such as `DOMAIN-SUFFIX`, `DOMAIN-KEYWORD`, `IP-CIDR`, `RULE-SET`, `GEOIP`, and `FINAL`. Name rule files by content and scope, for example `domains_youtube.list`, `ips_antifilter.list`, or `sr_ru_basic.conf`. Keep shell snippets POSIX-friendly where practical, but the workflow currently relies on common Ubuntu tools such as `curl`, `sed`, `awk`, `grep`, `sort`, `jq`, `dig`, and `python3`. Preserve Russian user-facing comments unless intentionally updating documentation.

## Testing Guidelines

No formal test framework is present. Validate changes by checking generated files for malformed or duplicate rules. Useful checks include:

- `rg -n "raw/\\.githubusercontent|github.com/.*/blob/" conf rules` to catch invalid raw URLs.
- `rg -n "^[^#].*,$|,,|  +" conf rules` to find suspicious rule formatting.
- Run the GitHub workflow after generator changes and confirm it commits only expected files.

## Commit & Pull Request Guidelines

Recent manual commits use short imperative summaries, for example `Revise proxy group and update rule set URL` or `Update README.md`. Automated workflow commits use `Auto: обновление сгенерированных файлов [skip ci]`; keep that exact form for generated updates. Pull requests should describe the affected configs or rule lists, explain any source URL changes, and note whether the generator workflow was run. Include screenshots only for documentation or user-facing page changes.

## Security & Configuration Tips

Do not commit secrets. `modules/Certificate.module` is generated from `CA_PASSPHRASE` and `CA_P12` GitHub secrets, so update those values in repository secrets, not in tracked files. Treat external rule sources as untrusted inputs and keep filters explicit when adding new `curl` pipelines.
