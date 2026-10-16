# dsh-superlocalmemory

A thin [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness) (`dsh`) bundle that connects [SuperLocalMemory](https://github.com/qualixar/superlocalmemory) to the harness over stdio MCP.

SuperLocalMemory is a local-first memory layer for AI agents: one memory that any harness speaking MCP can share. This bundle contains no memory logic. It is one configuration row for DeepSeek Harness's official MCP client plugin (`@deepseek-ai/dsh-mcp-client`) that launches `slm mcp`, so SuperLocalMemory's tools show up in the harness as `mcp__slm__<tool>`.

## Prerequisites

1. DeepSeek Harness (`npx @deepseek-ai/dsh web`, see the [harness README](https://github.com/deepseek-ai/deepseek-harness#run)).
2. SuperLocalMemory installed so that `slm` is on your `PATH`:

   ```sh
   npm install -g superlocalmemory
   # or: pipx install superlocalmemory
   slm --version
   ```

## Install

```sh
dsh plugin --profile web add dsh-superlocalmemory
```

Until the package is on npm, install from a clone instead:

```sh
git clone https://github.com/qualixar/dsh-superlocalmemory.git
dsh plugin --profile web add ./dsh-superlocalmemory
```

Restart `dsh web`. The bundle is appended to the profile's bundle list and its patch inserts one MCP client row:

```yaml
- insert:
    - id: memory-superlocalmemory
      name: '@deepseek-ai/dsh-mcp-client'
      config:
        serverName: slm
        transport: stdio
        command: slm
        args: [mcp]
```

If you prefer not to install a bundle, copy that row into `$DSH_HOME/profiles/<name>/cordis.patch.yml`, or pass the file once with `dsh web --patch ./cordis.patch.yml`.

## Verify

1. Open **Settings > Plugins > Plugin list**. A `mcp-client` entry with id `memory-superlocalmemory` should show as Mounted and Enabled.
2. Start a session. Initial discovery is asynchronous, so wait for the `mcp__slm__...` tools to appear.
3. In one session ask the agent to remember a unique fact, then in a new session ask it to recall that fact. The harness's own [memory guide](https://github.com/deepseek-ai/deepseek-harness/blob/main/docs/user/guide/mcp-memory.md) describes this write, fresh-session recall check.

You can also confirm the server on its own: `slm mcp` should start and wait for MCP messages on stdin.

## Changing the command

If `slm` is not on the `PATH` that the harness sees, set `command` to the absolute path of the executable (`which slm`).

## Licence

This bundle is MIT licensed (see `LICENSE`). It only contains configuration. SuperLocalMemory itself is a separate project under AGPL-3.0-or-later with its own licence terms; installing it is your choice and is governed by its repository.

SuperLocalMemory is built by [Varun Pratap Bhardwaj](https://github.com/varun369) as part of the Qualixar AI Reliability Engineering research initiative. This bundle is not affiliated with or endorsed by DeepSeek.
