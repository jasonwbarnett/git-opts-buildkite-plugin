# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Buildkite plugin that configures git options for `git clone` and `git fetch` operations. The plugin allows users to configure:
- Clone depth (`depth`)
- Git filter options (`filter`)
- Sparse checkout (`sparse-checkout`)
- Sparse checkout paths (`sparse-checkout-paths`)

## Architecture

The plugin follows standard Buildkite plugin architecture:

1. **plugin.yml** - Defines the plugin metadata and configuration schema
2. **hooks/pre-checkout** - Hook script that runs before git checkout, sets environment variables based on plugin configuration
3. **lib/plugin.bash** - Shared bash library containing helper functions for reading plugin configuration

## Key Components

### Environment Variable Mapping
The plugin works by mapping its configuration options to Buildkite's internal git environment variables:
- `BUILDKITE_PLUGIN_GIT_OPTS_DEPTH` → `BUILDKITE_GIT_CLONE_DEPTH`
- `BUILDKITE_PLUGIN_GIT_OPTS_FILTER` → `BUILDKITE_GIT_CLONE_FILTER`
- `BUILDKITE_PLUGIN_GIT_OPTS_SPARSE_CHECKOUT` → `BUILDKITE_GIT_SPARSE_CHECKOUT`
- `BUILDKITE_PLUGIN_GIT_OPTS_SPARSE_CHECKOUT_PATHS` → `BUILDKITE_GIT_SPARSE_CHECKOUT_PATHS`

### Helper Functions in lib/plugin.bash
- `prefix_read_list()` - Reads array values from environment variables
- `plugin_read_list()` - Wrapper for reading plugin-specific arrays
- `prefix_read_list_into_result()` - Reads arrays into a global `result` variable
- `plugin_read_list_into_result()` - Plugin-specific wrapper for above
- `plugin_read_config()` - Reads single configuration values

## Development Commands

### Testing
Currently, there are no test files in the repository. When implementing tests:
- Use bash testing frameworks like BATS for testing shell scripts
- Test the pre-checkout hook's behavior with different configurations
- Verify environment variable mappings work correctly

### Linting
For bash scripts, use:
```bash
shellcheck hooks/pre-checkout lib/plugin.bash
```

### Plugin Usage Example
```yaml
steps:
  - command: "echo 'Hello World'"
    plugins:
      - jasonwbarnett/git-opts#v1.0.0:
          depth: "1"
          filter: "blob:none"
          sparse-checkout: true
          sparse-checkout-paths:
            - "src/"
            - "docs/"
```

## Important Notes
- All bash scripts use `set -euo pipefail` for error handling
- The plugin prefix "GIT_OPTS" is used for all environment variables
- Array configurations can be specified either as a single value or as an indexed array (_0, _1, etc.)