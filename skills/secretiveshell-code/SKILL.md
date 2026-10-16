---
name: secretiveshell-code
description: Apply SecretiveShell's programming policies whenever designing, writing, refactoring, debugging, or reviewing code and application architecture, in any language. Prioritize human-readable logic, clear responsibility ownership, expressive data models, dedicated configuration, and simple idiomatic structure. Python and TypeScript services are modules exposing functions. Use across programming tasks, not only explicit style requests.
---

# Programming policies

Make software easy for a human to understand, navigate, and change correctly. These policies
apply across applications and languages, from a small script to a larger system. Scale the
structure to the task; clarity is the objective, not a prescribed number of files or layers.

Honor the user's current task and explicit repository requirements. Apply these preferences
to new and changed code without expanding a narrow change into an unrelated rewrite. Match
language idioms and required tooling, but do not copy dense code merely for consistency.

## 1. Make the logic read in order

Write a visible sequence of meaningful steps. Use descriptive names, intermediate values,
and blank lines so a reader can follow the operation without mentally unpacking expressions.
Prefer a few clear statements over combining lookup, fallback, conversion, and side effects
into one expression. Simple expressions and comprehensions are welcome when immediately clear.

Use whitespace deliberately: separate validation, lookup, transformation, and effects into
visible blocks, and expand multi-part calls, objects, and declarations. Formatting a deeply
nested expression across more lines is only a first step. If the reader must track several
levels of construction, callbacks, or conditional values at once, name the meaningful parts
before composing them. For UI code, name cohesive sections or components before assembling
the screen. Keep small, self-evident expressions inline.

Use guard clauses for invalid input, missing resources, and cases to skip. Return, raise, or
continue early, then keep the normal path flat. Use ordinary branches and loops when they
make decisions clearer. Preserve error semantics; readability must not hide failures.

Keep a cohesive operation together. Extract a function when its name explains a meaningful
subtask or hides details the caller should not need. Avoid splitting an understandable flow
into tiny helpers that require constant jumping between definitions. Judge readability by
mental effort, not line count. Comments explain constraints or mark useful phases; they do
not compensate for opaque code.

Keep each function at a consistent level of detail. An orchestrator should show the stages
of an operation; a substantive branch should have a named operation when it has its own
validation, transformation, or side effects. A dispatcher should make the supported actions
easy to see without embedding every action's implementation. Keep related operations nearby
and preserve shared authorization, transaction boundaries, and error behavior when extracting
them. Avoid one-line forwarding helpers and generic callback machinery introduced only to
make a function shorter. See [structural examples](references/structure.md).

## 2. Give each responsibility a clear owner

Organize code around recognizable responsibilities with small, operation-specific interfaces.
Callers should express what they need without repeating how configuration is loaded, a request
is encoded, or a record is stored. Reuse an existing owner when one already fits.

Keep orchestration visible: check prerequisites, call the relevant operations, handle results.
Place implementation details with their owner. Avoid both a single module mixing unrelated
concerns and layers that merely forward calls. Add a boundary when it clarifies responsibility,
isolates meaningful variation, or protects an invariant—not to satisfy an architectural template.

In **Python and TypeScript, the file is the service**: modules expose functions and appropriate
shared values. Avoid service classes, static-only class wrappers, singleton classes, and service
inheritance hierarchies. Data-model classes and library client objects are fine. Manage resource
lifetimes explicitly through the language's normal mechanisms.

In other languages, use their idiomatic units—modules, packages, structs, or classes—to achieve
the same clear ownership. Do not export the Python/TypeScript class preference as a universal
ban. A service here is a responsibility in code; it does not imply a separately deployed process.

## 3. Make configuration ownership explicit

An application with runtime settings has a dedicated configuration owner, even when the only
setting is a CLI database path. Use `config.py` or `config.ts` in Python/TypeScript, an existing
configuration module, or the idiomatic equivalent in another language/framework.

That owner defines defaults, loads settings, resolves override precedence, normalizes values,
and validates the resolved configuration. CLI parsers, HTTP entrypoints, and UI code handle
their input mechanisms and pass overrides to it. Startup uses the result to assemble the
application. Consumers do not scatter environment reads, default paths, or configuration policy
through business operations. Reuse shared schemas in their owning package where applicable.

Keep this small: a resolver and a typed settings value are often sufficient. A configuration
owner does not require mutable global state or a configuration framework. Ordinary operation
arguments remain operation arguments; applications without runtime settings need no empty
configuration module. See the [configuration example](references/readability.md#configuration-and-cli-ownership).

## 4. Model facts and valid states expressively

Before choosing fields, consider what facts exist, which states are valid, and whether a more
expressive representation would simplify the consuming code. Use the language's domain types
and the project's schemas to communicate meaning and enforce useful constraints.

Store authoritative facts and derive redundant values where practical. If completion time is
meaningful and always known, a nullable completion timestamp records both whether and when;
completion can be derived from its presence. Avoid independently writable flags that can
contradict the facts they summarize. If a cached projection is necessary, make its source and
update responsibility explicit.

Represent mutually exclusive states with an enum or tagged/discriminated union when appropriate.
Associate state-specific data with the state that requires it. Distinguish absent, unknown,
empty, and zero when their meanings differ. A boolean is appropriate for a genuinely binary
fact; do not invent timestamps or richer structures without domain meaning.

Check transitions as well as static shapes. Consider reopening or repetition when relevant;
a current-state timestamp is not an event history. Preserve unknown information rather than
fabricating values. Account for compatibility and migration before changing persisted or public
representations. Explain consequential modeling choices briefly in terms of the facts they capture.

## 5. Use the simplest structure that meets the actual need

Prefer direct functions, explicit mappings, and ordinary control flow. Introduce an abstraction
when it removes meaningful repetition, names a real concept, or supports actual variation.
Avoid speculative frameworks, service containers, generic registries, and configurable machinery
for a single straightforward operation. Do not replace an already suitable dependency or stack
just to imitate a reference project.

Keep dependencies and side effects understandable. Put I/O and resource management with clear
owners; pass changing inputs such as time explicitly when logic depends on them. Validate
untrusted input at the relevant boundary and pass well-defined values inward. Make failures and
state transitions visible, and protect multi-step changes where partial completion would violate
the operation's contract. Apply these needs at the task's scale rather than adding blanket layers.

## 6. Verify behavior and inspect the design

Follow the repository's required checks and test observable behavior where the change needs it,
especially state transitions, time boundaries, contracts, and failure cases. Passing tests alone
does not establish that the code follows these policies.

Before finishing, inspect whether the main path is readable, responsibilities have actual owners,
runtime configuration is centralized, and representations capture facts without contradictory
state. In Python/TypeScript, check that service behavior lives in module functions. A file named
`config` does not count if defaults and resolution still live elsewhere.

Inspect the largest or most deeply nested changed functions and expressions after formatting.
Can their purpose and main stages be understood without tracing inner callbacks or expanding
every helper? If not, simplify their structure. Prefer meaningful names and cohesive extraction
over a fixed line-length, nesting-depth, or function-size quota. Tests and formatter success
are evidence of correctness and layout, not proof of understandable composition.

Report meaningful trade-offs or explicit constraints that prevented the intended structure.
Preserve existing behavior unless the task authorizes a change. A style preference is not
permission to broaden scope or perform external actions.

## Supporting guidance

Read [implementation examples](references/readability.md) when a concrete illustration helps.
Read [language-specific formatting](references/language-style.md) for Python layout and local
formatting details. Read [evidence](references/evidence.md) only when tracing or revising the
profile. The user's stated policies take precedence over inferred habits in historical samples.
