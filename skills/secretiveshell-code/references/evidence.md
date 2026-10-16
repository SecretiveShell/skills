# Code-style evidence

## Explicit user guidance

The user wants this skill to apply whenever they program, across applications and languages.
The main entrypoint therefore expresses general code and architecture policies; concrete
Python/TypeScript preferences remain explicit specializations. Formatting evidence is kept
in a separate optional reference. Automatic skill selection is enabled.

The user clarified that the main concern is model-generated code that is dense and hard to
follow. They explicitly want clear human-readable logic and guard clauses. Their example of
a configuration helper illustrates a broader preference for organizing behavior into small
services with understandable interfaces. They expressly clarified that this is not a
specific request for a minimal config helper. Do not turn the example into a universal
global-config or singleton rule. These statements carry more weight than repository inference.

They further specified that Python and TypeScript services should avoid classes: make a file
such as `config.py` the service. Apply this to service modules generally, including `config.ts`
in TypeScript. Keep configuration access with its owning module. This overrides historical
class-based service patterns; it does not prohibit data-model classes or using SDK clients.

They also explicitly want agents to consider correct, idiomatic model representations and
more expressive encodings of the same data. Their example is `complete: bool` versus
`completed_at: datetime | null`. The general preference is to model meaningful facts and
derive redundant values, selecting a representation that fits the actual domain. It is
not an instruction to replace every boolean with a timestamp.

The [implementation examples](readability.md) illustrate this clarified guidance.

## Historical research

The complex Queuehouse test exposed a second gap. A final formatter pass expanded dense
JavaScript and CSS, but large nested DOM expressions and a substantial mutation dispatcher
remained difficult to follow. The policies now distinguish multiline formatting from readable
composition and call for meaningful intermediate values and cohesive action functions where
needed. This must not become a fixed size quota or automatic extraction of tiny helpers.

Forward testing found an important gap: the Lendkit agent used module-level services but
kept its default database path in `cli.py` and created no configuration module. The user
identified that omission after the test. The skill now explicitly requires a dedicated
configuration owner for runtime settings, including CLI-only applications, and distinguishes
CLI syntax from configuration policy. This is a correction from observed behavior, not an
inference from the user's old repositories. Behavioral test success alone did not catch it.

Reviewed 2026-10-13. The user clarified that this skill should capture spacing, formatting,
structures, and coding patterns. Architecture and workflow guidance from the first draft
were removed; the later service-organization preference above was stated directly by the user.
MCP-Bridge is the main sample; Wave supplies only explicit local formatting
requirements. Do not infer the user's personal writing habits from agent-authored Wave code.

## Authorship and confidence

GitHub identifies the account as SecretiveShell / TerminalMan. Author-filtered commit history
was checked for `openai_clients/utils.py`, `config/final.py`,
`tool_mappers/mcp2openaiConverters.py`, and `mcp_clients/McpClientManager.py`.
The first two examples below were also read at the indicated user-authored commits.
This improves attribution, but a repository snapshot can still contain others' contributions.

### Guard clauses and logical whitespace

[utils.py at e17e17f](https://github.com/SecretiveShell/MCP-Bridge/blob/e17e17f0b6c27f368e770a1117511c746cb03c1c/mcp_bridge/openai_clients/utils.py)
contains `call_tool`: input checks return early; a blank line separates session lookup from
the missing-session check; JSON parsing has its own try/except block; the final awaited
call is returned directly. Its sibling helper gathers tools through explicit nested loops
and skips unavailable sessions with `continue`.

This supports a preference for visible steps and whitespace between them. It does not
establish that every function should return `None` on error or that loops always beat
comprehensions.

### Explicit construction

[mcp2openaiConverters.py at f458d77](https://github.com/SecretiveShell/MCP-Bridge/blob/f458d77aaecf97629bdb0f14ecc2334ea4df7b02/mcp_bridge/tool_mappers/mcp2openaiConverters.py)
has a small typed conversion function, a one-sentence docstring, a blank line before its
body, and a directly returned constructor with named arguments. A nested dictionary places
each field on its own line, uses double quotes, and has trailing commas.

### Models, classes, and comments

These files were read at MCP-Bridge revision `df1dcc58cac7bf2a4ead5f5630eb970f42999c57`:

- [config/final.py](https://github.com/SecretiveShell/MCP-Bridge/blob/df1dcc58cac7bf2a4ead5f5630eb970f42999c57/mcp_bridge/config/final.py):
  typed Pydantic fields, named defaults/descriptions, literal alternatives, and expanded calls.
- [McpClientManager.py](https://github.com/SecretiveShell/MCP-Bridge/blob/df1dcc58cac7bf2a4ead5f5630eb970f42999c57/mcp_bridge/mcp_clients/McpClientManager.py):
  short methods, direct transport-selection branches, explicit startup calls, and early returns.
- [config/__init__.py](https://github.com/SecretiveShell/MCP-Bridge/blob/df1dcc58cac7bf2a4ead5f5630eb970f42999c57/mcp_bridge/config/__init__.py):
  brief phase comments, typed intermediate variables, direct loops, and visible configuration
  loading/merging/construction steps.
- [modelSelector.py](https://github.com/SecretiveShell/MCP-Bridge/blob/df1dcc58cac7bf2a4ead5f5630eb970f42999c57/mcp_bridge/sampling/modelSelector.py):
  both a compact comprehension and an ordinary loop with intermediate values. This is
  counterevidence to a blanket prohibition on comprehensions. Its spacing and quotes also
  show why the main preferences should not be represented as perfectly uniform rules.

The `AbstractClient.py`, `StdioClient.py`, chat-completion modules, routers, and application
assembly were also sampled. Their inconsistent imports, long lines, and debug remnants are
not sound foundations for personal mandates.

Cross-check: [MCP-searxng search.py](https://github.com/SecretiveShell/MCP-searxng/blob/7aba5ed80656818132ebaff35bde50c9c6d757be/src/mcp_searxng/search.py)
also combines straightforward models, named intermediate values, loops, double quotes, and
blank lines between operations. Other project samples were not used to override the stronger
MCP-Bridge authorship evidence.

## Illustrative example

This example is newly written to demonstrate the profile; it is not copied user code or a
claim that the user prefers these exact names. It shows four-space indentation, double
quotes, top-level spacing, a concise docstring, a guard clause, logical blocks, and explicit
construction. In a real repository, use its existing model and error contract.

```python
from pydantic import BaseModel


class ToolSummary(BaseModel):
    name: str
    description: str


def describe_tool(name: str, description: str | None) -> ToolSummary:
    """Build a display summary for a named tool."""

    if not name:
        raise ValueError("Tool name is empty")

    text = description if description is not None else "No description available"

    return ToolSummary(
        name=name,
        description=text,
    )
```

## Boundaries of the evidence

High confidence in the sample: four-space Python indentation; predominantly double quotes;
logical blank lines; descriptive snake_case function names; named model fields; explicit
intermediate values; early returns/continues; short practical comments.

Variable: exact import order, module filename casing, optional-type spelling, wrapping style,
docstring capitalization, and completeness of annotations. No personal hard line-length
limit or maximum function length was established. Follow the target formatter and adjacent
code for these choices.

Wave's supplied `AGENTS.md` explicitly establishes two spaces, single quotes, no semicolons,
100 columns, and Biome. Keep those requirements local to Wave and similarly configured
projects. The Python samples do not justify transferring their punctuation to TypeScript.
