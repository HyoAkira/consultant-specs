---
name: consultant-model-tools
description: Declare OpenCode's built-in tools for any model, and use tools.opencode to inspect models and run work. Use when a model session has no read/write/edit/glob/grep/shell/webfetch tools, when configuring a custom provider entry, or when you need to find a model's exact reference.
---

# Model Tools

OpenCode builds what a model receives from its declared capabilities and limits. A custom entry that declares neither relies on fallback assumptions instead of facts, so the entry cannot be trusted to match the server.

## 1. The model is not offered OpenCode's tools

Symptom: a session comes up with no `read`, `write`, `edit`, `glob`, `grep`, `shell` or
`webfetch`, even though those tools exist in OpenCode.

Check in this order:

- the model entry exists and its `modelID` matches what the server serves;
- the entry declares `capabilities.tools: true`;
- no `permissions` rule denies the tool;
- the server's chat template can express a tool call.

Declare the capabilities on the provider's model entry:

```jsonc
{
  "providers": {
    "<provider>": {
      "models": {
        "<model-key>": {
          "modelID": "<server-model-id>",
          "name": "<display name>",
          "capabilities": { "tools": true, "input": ["text"], "output": ["text"] },
          "limit": { "context": <real-window>, "output": <real-output> }
        }
      }
    }
  }
}
```

- `capabilities.tools: true` states that the server accepts tool definitions. Declare it
  rather than leave tool support to a fallback that was never checked against the server.
- `input` / `output` must list the modalities the server really supports. Claiming
  `image` input for a text-only model breaks the request instead of failing cleanly.
- `limit.context` must be the server's real window, never the 200,000-token fallback.
  An inflated window causes silent truncation.
- This entry augments the globally defined provider, so endpoint settings from the
  global config still apply. Only the model entry is added here.

Tool availability is separate from tool permission. Built-in tools are governed by
`permissions` (actions `read`, `edit`, `glob`, `grep`, `shell`, `webfetch`,
`websearch`, `execute`), and the default base policy allows everything. No extra allow
rule is needed.

Do not deny `execute` with resource `*` on an agent that must reach MCP tools: that
removes Code Mode and every tool reachable only through it.

## 2. Seeing which models exist

Inside Code Mode, use the `opencode` namespace from `execute`:

```js
// Every provider. `limit` maxes out at 100.
const r = await tools.opencode.models({ limit: 100, all: true });
return r.providers.map(p => p.id + " -> " + p.models.map(m => m.id).join(", "));
```

Filter to one provider, and use `query` to turn a name the user mentioned into an
exact reference:

```js
return await tools.opencode.models({ provider: "<provider>", query: "<name>" });
```

Read the returned `id` as `<providerID>/<modelID>`. Keep the whole ID: a provider ID
ends at the first `/`, while a model ID may contain further slashes.

## 3. Model IDs go stale

Some servers report a path or transient ID as the model ID, and serve one model at a
time. Every relaunch with a different model changes the ID, so any catalog entry
written by hand points at a model the server no longer has loaded. The session then
fails on the first request while the model still looks available in the catalog.

- Prefer a stable catalog key with the server's current ID in `modelID`. The
  selectable reference stays stable even when the path changes.
- Before dispatching, confirm the ID from `tools.opencode.models`. Never reconstruct
  an ID from memory or from an earlier conversation.
- If the model the user wants is absent, report that and offer to add the entry rather
  than substituting a different model.

## 4. Running work on a model

Resolve the reference first, then dispatch:

```js
return await tools.opencode.models({ provider: "<provider>" });
```

Ask which model to use unless the user named one or the choice is obvious. Model
selection is scoped to the location, so a model available in one project need not be
available in another. After dispatch, read the session's messages to confirm the model
actually answered; a failure at the provider surfaces there, not at dispatch time.

## 5. Server-side tool calling

Offering a tool and being able to call it are different problems. Whether a model can
*call* a tool belongs to the server, not to OpenCode: the server needs a tool-aware
chat template, and a template without tool support answers without ever emitting a
call. If the tools are offered and calls still never appear, inspect the template
before changing configuration again. Distinguish the two failures by checking the
tool list in a session first, and the response shape second.

## Reporting

State the exact `provider/model` used, and say whether the session had the built-in
tools. Report what you observed rather than inferring a cause: a session with no
tools points at configuration, while a session with tools that never calls them
points at the server or the template.
