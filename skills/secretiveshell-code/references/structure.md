# Readable composition and function structure

These examples illustrate how to reduce the amount a reader must keep in mind. They are
not required frameworks, file layouts, or helper counts. Preserve execution order, transaction
ownership, permissions, and failure behavior when restructuring actual code.

## Name meaningful parts before composing them

A formatter can wrap a large nested expression while leaving its structure hard to follow.
For a view built through DOM functions, assemble recognizable pieces before the outer layout:

```javascript
function renderContactList(contacts) {
  const heading = element("h2", {}, "Contacts");
  const items = [];

  for (const contact of contacts) {
    const name = element("strong", {}, contact.displayName);
    const email = element("span", {}, contact.email);
    const item = element("li", {}, name, email);

    items.push(item);
  }

  const list = element("ul", {}, ...items);
  return element("section", {}, heading, list);
}
```

Here `element` is an existing DOM helper that treats strings as text. The same idea applies
to JSX, query construction, serializers, and configuration objects. For small expressions,
inline construction or a simple map may be clearer; do not introduce a variable for every
literal or extract each element into a function. Extract a whole coherent section when it
would otherwise obscure the surrounding view.

## Separate dispatch from substantive operations

An orchestrator can keep shared checks and transaction ownership visible while named
operations handle the work of each action. This illustrative shape assumes the repository
already provides the referenced types and functions:

```python
def apply_change(database, actor, item_id, command):
    with transaction(database):
        item = load_item(database, item_id)
        require_access(actor, item)
        require_current_version(item, command.version)

        if command.action == "approve":
            result = approve_item(database, actor, item, command)
        elif command.action == "withdraw":
            result = withdraw_item(database, actor, item, command)
        else:
            raise ValueError("Unsupported action")

        record_change(database, actor, result)
        return result
```

Each named operation should contain meaningful work, not immediately forward to another
helper. Keep action-specific permission checks with the applicable action and keep all
writes in the intended transaction. Use explicit project types in real code; their omission
here keeps the example focused on control flow.

A long function with a single clear sequence can remain intact. A short function that hides
several unrelated steps in nested expressions can still need restructuring. Judge the shape
by whether the reader can follow the work and locate the rules governing it.
