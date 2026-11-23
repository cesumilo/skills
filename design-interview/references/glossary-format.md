# Glossary Entry Format

One entry per concept, in alphabetical order, one level-two heading per term. Each entry has the term, a precise definition scoped to this project, and the words to avoid.

```markdown
## Customer
The billing entity that holds a subscription and pays invoices.
One Customer may have many Users, but Users never pay directly.
_Avoid_: account, client, organization

## User
A person who authenticates and acts inside the product.
_Avoid_: member, account holder
```

## Rules

- Define the word as the project uses it, not as a dictionary does.
- Define product and domain terms, not code identifiers. A class, function, or type name is already defined in the code. Add a code term only when it carries product meaning or is overloaded, and then define the concept, not the class.
- A deliberate layer pair uses two entries, one for the product name and one for the technical name. Each entry names the other, and neither lists the other under `_Avoid_`.
- List every synonym or near-synonym under `_Avoid_`.
- Keep each entry to two or three lines.
- When a definition changes, update the entry in place.
