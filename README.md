# Conventional Commits validator
A Git pre-commit hook which validates commit messages using [Conventional Commits](https://www.conventionalcommits.org/) standard
## Requirements
To use this hook, you need to have [jq](https://stedolan.github.io/jq/download/) installed

## Installation

### Existing project
In existing project:
```
cd .git/hooks
curl https://raw.githubusercontent.com/mint-hosting/git-hook-conventional-commits-validator/master/commit-msg --output commit-msg
curl https://raw.githubusercontent.com/mint-hosting/git-hook-conventional-commits-validator/master/commit-msg-config.json --output commit-msg-config.json
chmod a+x commit-msg
```

### Globally using Git Templates
If you want, you can install this hook globally, so it's going to be available in every repository your initialize. For example:

```
git config --global init.templatedir '~/.git-templates'
mkdir ~/.git-templates/hooks
curl https://raw.githubusercontent.com/mint-hosting/git-hook-conventional-commits-validator/master/commit-msg --output commit-msg
curl https://raw.githubusercontent.com/mint-hosting/git-hook-conventional-commits-validator/master/commit-msg-config.json --output commit-msg-config.json
chmod a+x commit-msg
```

## Usage
Once you download files you need to reinitialize your repo with `git init`. 

The hook will look for a configuration file in the following locations (in order):
1. The root of your Git project
2. `GIT_CONVENTIONAL_COMMIT_VALIDATION_CONFIG`: you can also set a custom location for your configuration file by setting the `GIT_CONVENTIONAL_COMMIT_VALIDATION_CONFIG` env variable.

The configuration contains the following items by default:
```
{
    "enabled": true,
    "revert": true,
    "length": {
        "min": 1,
        "max": 52
    },
    "types": [
        "build",
        "ci",
        "docs",
        "feat",
        "fix",
        "perf",
        "refactor",
        "style",
        "test",
        "chore"
    ]
}
```
If you want to disable hook temporarily just change `enabled` to `false`.

You can also add new commit types or adjust the length of messages in this configuration, which allows you to have different configurations per project and a sane "default" in global configuration.

