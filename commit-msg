#!/bin/bash

# Check if jq is installed
function checkIfJqExists()
{
    if ! [ -x "$(command -v jq)" ]; then
        echo -e "\`commit-msg\` hook cannot be run because jq is missing."
        exit 1
    fi
}

# Check if config file exists
function checkIfConfigFileExists()
{
    if [[ ! -f "$CONFIG" ]]; then
        echo -e "Hook config file is missing. To learn how to set one, please see a README."
        exit 0
    fi
}

# Set configuration path
# Configuration can either be part of existing repo or a path stored in env variable
# Local configuration overrides global configuration
function setConfigurationPath()
{
    localConfig="$PWD/.git/hooks/commit-msg-config.json"
    
    if [ -f "$localConfig" ]; then
        CONFIG=$localConfig
    elif [ -n "$GIT_CONVENTIONAL_COMMIT_VALIDATION_CONFIG" ]; then
        CONFIG=$GIT_CONVENTIONAL_COMMIT_VALIDATION_CONFIG
    fi
}

# Loads configuration from a file
function loadConfiguration()
{
    isEnabled=$(jq -r .enabled "$CONFIG")

    if [[ ! $isEnabled ]]; then
        exit 0
    fi

    revert=$(jq -r .revert "$CONFIG")
    types=($(jq -r '.types[]' "$CONFIG"))
    minimumLength=$(jq -r .length.min "$CONFIG")
    maximumLength=$(jq -r .length.max "$CONFIG")
}

# Dynamically build regular expression used for commit message validation
function buildRegex()
{
    loadConfiguration
    
    regexp="^[.0-9]+$|"

    if $revert; then
        regexp="${regexp}^([Rr]evert|[Mm]erge):? )?.*|^("
    fi

    for type in "${types[@]}"
    do
        regexp="${regexp}$type|"
    done

    regexp="${regexp%|})(\(.+\))?: "
    regexp="${regexp}.{$minimumLength,$maximumLength}$"
}

# Print an error message which explains what is wrong with the commit message itself
function displayOutput()
{
    NOCOLOR='\033[0m'
    RED='\033[0;31m'
    ORANGE='\033[0;33m'
    CYAN='\033[0;36m'
    LIGHTRED='\033[1;31m'
    YELLOW='\033[1;33m'

    commitMessage=$(git log -1 HEAD --pretty=format:%s)

    echo -e "\n${RED}[Invalid Commit Message]${NOCOLOR}"
    echo -e "------------------------"
    echo -e "${ORANGE}Allowed types:${NOCOLOR} ${types[@]}"
    echo -e "${ORANGE}Max length (first line):${NOCOLOR} $minimumLength"
    echo -e "${ORANGE}Min length (first line):${NOCOLOR} $maximumLength"
    echo -e "${ORANGE}Your commit message:${NOCOLOR} $commitMessage"
    echo -e "${ORANGE}Length:${NOCOLOR} $(echo $commitMessage | wc -c)"
}

# Init the hook
setConfigurationPath
checkIfConfigFileExists
checkIfJqExists
loadConfiguration

# Get the first line of a commit message
INPUT_FILE=$1
START_LINE=`head -n1 $INPUT_FILE`

buildRegex

# Validate commit message agains regexp
if [[ ! $START_LINE =~ $regexp ]]; then
    displayOutput
    exit 1
fi
