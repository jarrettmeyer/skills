---
name: uuid
description: Generate a new UUID (universally unique identifier). Use this skill whenever the user asks for a UUID, GUID, unique identifier, random ID, or needs to generate one or multiple unique identifiers for any purpose - databases, file names, session tokens, test data, or any other use case requiring unique IDs.
model: haiku
---

# uuid

## Single UUID

By default, generate a single UUID. This may be invoked by slash command: `/uuid`.

```python
import uuid
value = str(uuid.uuid4())
print(value)
```

## Multiple UUIDs

This skill can also be used to generate multiple UUIDs. The user may requested this with a slash command: `/uuid <count>`.

```python
import uuid
count = 5 # Replace with user's requested count
for _ in range(count):
  value = str(uuid.uuid4())
  print(value)
```
