# Language-specific formatting

These are secondary preferences inferred from code samples. Explicit user preferences and
repository tooling take precedence. They refine the general policies, not define them.

## Python spacing and layout


- Use four spaces for indentation and predominantly double-quoted strings. Use f-strings
  for straightforward interpolation. Triple double quotes are the normal docstring form.
- Separate top-level functions and classes with two blank lines as the normal baseline.
  Inside functions, use one blank line between meaningful steps: check inputs, find a
  resource, parse data, perform the operation, return the result. Keep statements belonging
  to the same step together; do not space out every line.
- Keep short calls and signatures on one line. Expand constructors and larger calls into
  indented blocks when that makes the fields easier to scan. Prefer named arguments for
  structured objects and configuration; keep ordinary short helper calls compact.
- Expanded dictionaries and constructors commonly put each field or argument on its own
  line, with trailing commas and the closing delimiter aligned with the opening statement.
  A wrapped function signature can keep several short parameters on the continuation line;
  do not force every parameter onto a separate line.
- Group related imports, and use parenthesized multiline imports for longer name lists.
  The samples mix import ordering: follow local tooling rather than inventing a personal
  standard-library/third-party ordering rule. Both package-qualified and sibling-relative
  imports occur; preserve the target module's convention.
- There is no established personal hard line-length limit. Use the repository's setting.
  Do not emulate incidental whitespace, trailing spaces, or unusually long historical lines.

## Names and data declarations

In Python, use `snake_case` for functions, arguments, and ordinary variables, and `PascalCase`
for classes and models. Names usually describe the operation or role directly:
`call_tool`, `get_client`, `find_best_model`, `tool_call_args`, `server_config`.
Short locals such as `client`, `session`, `result`, and `msg` fit when context is clear.
Private Python helper functions commonly start with `_`.

Use parameter and return annotations to expose meaningful contracts. Typed collections,
Pydantic models, `Literal` alternatives, and optional values recur. Local annotations are
useful for accumulators and state whose type is otherwise unclear; do not annotate every
obvious assignment. The samples mix `Optional[T]` and `T | None`; follow nearby code and
the supported Python version. No universal preference for one was established.

Pydantic model classes generally read as a straightforward list of typed fields, using
`Field` for defaults and descriptions. Keep field construction explicit. Do not introduce
metaprogramming just to shorten a few declarations.

Historical filenames mix `snake_case`, camelCase, and PascalCase. Keep existing filenames
and local naming conventions instead of renaming modules to manufacture consistency.

## Comments and docstrings


Use short, plain comments beside the relevant code. Brief phase markers such as
`# merge the configs` and practical explanations of a library quirk both occur. Do not
replace them with ornamental banners, essay-length commentary, or mandatory formal prose.
Add them when they help divide or explain the work, not above every obvious statement.

A one-sentence docstring is a good default for a small helper. In many sampled functions,
one blank line follows the docstring before the implementation. Longer descriptions belong
where a non-obvious contract needs them; do not impose an Args/Returns template everywhere.
Use natural wording; inconsistent historical capitalization is not a requirement.

## TypeScript and other languages

Use the language and repository conventions for names, imports, punctuation, and formatting.
Wave specifically uses two-space indentation, single quotes, no semicolons, and a 100-column
Biome configuration. Those settings are local to Wave and similarly configured repositories.
Do not impose Python formatting or Wave tooling on other applications.
