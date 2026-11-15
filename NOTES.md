# Notes: PUT /users/:id

## The plan
I planned the change in plan mode before writing any code. The approved plan:
add `updateUser(id, { name, email })` to `db/store.js`, reusing `getUserById`
and returning `undefined` when the user is missing. In `routes/users.js`, add
`PUT /users/:id` that validates `name` and `email` (400 with the same message
as POST), calls the store, and returns 404 when nothing was found. I approved
the plan without changing it.

## Model choice
I used Haiku 5.5 for this work. It's a small, well-scoped change with a plan
already in place and existing patterns to copy, so a smaller, faster model was
enough. A larger model would be worth it if the endpoint had more moving parts,
such as partial updates or a real database.

## Commit split
Two commits, one per layer:
1. `db/store.js`: the `updateUser` helper, so the data change lands on its own.
2. `routes/users.js`: the route, which depends on the helper and reads cleanly
   against it.

I left the `NOTES.md` commit separate so the notes can describe the finished
code.

## What review caught
I reviewed the diff myself before committing. Things I checked:
- A non-numeric id (for example `/users/abc`) becomes `NaN`, which matches no
  user, so it returns 404 as intended.
- An empty `name` or `email` fails validation and returns 400 before any lookup.
- The updated user keeps its original id.

Gap I noticed and left alone: validation checks that fields are truthy, not
that they are non-empty strings, so a number or object would pass. POST has the
same behavior, so I kept the two consistent rather than fixing only PUT.
