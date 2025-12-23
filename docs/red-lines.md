# Red Lines

This document defines non-negotiable boundaries
for Plain Primitives.

It exists primarily to protect the future maintainer (myself).

## Non-negotiable rules

- Primitives must not make decisions.
- Primitives must not encode business or domain language.
- Primitives must not hide side effects.
- Primitives must not include policy or defaults that imply intent.

If something feels useful but violates one of these rules, it does not belong here.
