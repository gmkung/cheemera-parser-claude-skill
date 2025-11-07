# Common Parsing Patterns

Patterns and strategies for converting different types of statements into Cheemera belief sets.

## Pattern 1: Simple If-Then

**Pattern**: "If X, then Y"

**Structure**:

```json
{
  "scenario": {
    "type": "IF_THEN",
    "antecedents": [[{ "valence": true, "sentence": "X" }]],
    "consequences": [
      {
        "modal": "Always",
        "properties": [{ "valence": true, "sentence": "Y" }]
      }
    ]
  }
}
```

**Examples**:

- "If it rains, the ground is wet"
- "If user clicks button, form submits"
- "If temperature exceeds 100°C, water boils"

## Pattern 2: Compound Conditions (AND)

**Pattern**: "If X AND Y AND Z, then outcome"

**Structure**: All conditions go in the SAME antecedent array

```json
{
  "antecedents": [
    [
      { "valence": true, "sentence": "X" },
      { "valence": true, "sentence": "Y" },
      { "valence": true, "sentence": "Z" }
    ]
  ]
}
```

**Example**: "If user is logged in AND has permission AND resource exists, grant access"

## Pattern 3: OR Logic (Multiple Paths)

**Pattern**: "If X OR Y, then outcome"

**Strategy**: Create separate beliefs for each path

```json
{
  "beliefs": [
    {
      "scenario": {
        "type": "IF_THEN",
        "antecedents": [[{"valence": true, "sentence": "X"}]],
        "consequences": [{"modal": "Always", "properties": [{"valence": true, "sentence": "outcome"}]}]
      },
      "beliefUniqueId": "belief-001",
      ...
    },
    {
      "scenario": {
        "type": "IF_THEN",
        "antecedents": [[{"valence": true, "sentence": "Y"}]],
        "consequences": [{"modal": "Always", "properties": [{"valence": true, "sentence": "outcome"}]}]
      },
      "beliefUniqueId": "belief-002",
      ...
    }
  ]
}
```

**Example**: "If user is admin OR user is owner, allow deletion"
→ Two beliefs: one for admin, one for owner

## Pattern 4: Negation

**Pattern**: "If NOT X, then Y" or "If X, then NOT Y"

**Strategy**: Use `valence: false`

**Antecedent negation**:

```json
{
  "antecedents": [[{ "valence": false, "sentence": "X" }]]
}
```

**Consequence negation**:

```json
{
  "consequences": [
    { "modal": "Always", "properties": [{ "valence": false, "sentence": "Y" }] }
  ]
}
```

**Examples**:

- "If user is not verified, deny access" → antecedent: `{"valence": false, "sentence": "user is verified"}`
- "If account is frozen, user cannot withdraw" → consequence: `{"valence": false, "sentence": "user can withdraw"}`

## Pattern 5: Multiple Consequences

**Pattern**: "If X, then Y and Z and W"

**Structure**: All consequences in the SAME properties array

```json
{
  "consequences": [
    {
      "modal": "Always",
      "properties": [
        { "valence": true, "sentence": "Y" },
        { "valence": true, "sentence": "Z" },
        { "valence": true, "sentence": "W" }
      ]
    }
  ]
}
```

**Example**: "If order confirmed, send email AND update inventory AND charge payment"

## Pattern 6: Mutual Exclusion

**Pattern**: "X and Y cannot both be true"

**CRITICAL**: For MUTUAL_EXCLUSION, put mutually exclusive properties in separate **antecedent arrays**, NOT in consequences. Consequences should be empty `[]`.

**Structure**:

```json
{
  "scenario": {
    "type": "MUTUAL_EXCLUSION",
    "antecedents": [
      [{ "valence": true, "sentence": "X" }],
      [{ "valence": true, "sentence": "Y" }]
    ]
  }
}
```

**How it works**: The server automatically converts this to:

- "If X is true → Y is NOT true"
- "If Y is true → X is NOT true"

**Examples**:

- "A door cannot be both open and closed"

  ```json
  {
    "scenario": {
      "type": "MUTUAL_EXCLUSION",
      "antecedents": [
        [{ "valence": true, "sentence": "the door is open" }],
        [{ "valence": true, "sentence": "the door is closed" }]
      ]
    }
  }
  ```

- "A user cannot be both active and deleted"
- "Transaction cannot be both pending and completed"

## Pattern 7: Mutual Inclusion

**Pattern**: "If X is true, Y must also be true" (bidirectional dependency)

**CRITICAL**: For MUTUAL_INCLUSION, put mutually inclusive properties in separate **antecedent arrays**, NOT in consequences. Consequences should be left out.

**Structure**:

```json
{
  "scenario": {
    "type": "MUTUAL_INCLUSION",
    "antecedents": [
      [{ "valence": true, "sentence": "X" }],
      [{ "valence": true, "sentence": "Y" }]
    ]
  }
}
```

**How it works**: The server automatically converts this to:

- "If X is true → Y is true"
- "If Y is true → X is true"

**Examples**:

- "If a user has premium features, they must have an active subscription"

  ```json
  {
    "scenario": {
      "type": "MUTUAL_INCLUSION",
      "antecedents": [
        [{ "valence": true, "sentence": "user has premium features" }],
        [{ "valence": true, "sentence": "user has active subscription" }]
      ]
    }
  }
  ```

- "Married couples must both have spouse records"

## Pattern 8: Chained Logic

**Pattern**: "If X then Y, and if Y then Z"

**Strategy**: Create separate beliefs that chain together

```json
{
  "beliefs": [
    {
      "scenario": {
        "type": "IF_THEN",
        "antecedents": [[{"valence": true, "sentence": "X"}]],
        "consequences": [{"modal": "Always", "properties": [{"valence": true, "sentence": "Y"}]}]

      },
      "beliefUniqueId": "belief-001",
      ...
    },
    {
      "scenario": {
        "type": "IF_THEN",
        "antecedents": [[{"valence": true, "sentence": "Y"}]],
        "consequences": [{"modal": "Always", "properties": [{"valence": true, "sentence": "Z"}]}]

      },
      "beliefUniqueId": "belief-002",
      ...
    }
  ]
}
```

The inference engine will automatically deduce that X → Z

## Pattern 9: Constraints and Violations

**Pattern**: Rules that define violations or forbidden states

**Strategy**: Use consequences that indicate constraint violation

```json
{
  "scenario": {
    "type": "IF_THEN",
    "antecedents": [[{ "valence": true, "sentence": "invalid condition" }]],
    "consequences": [
      {
        "modal": "Always",
        "properties": [
          { "valence": true, "sentence": "the constraint is violated" }
        ]
      }
    ]
  }
}
```

**Example**: "If user tries to withdraw more than balance, transaction is invalid"
→ consequence: `{"valence": true, "sentence": "the transaction is invalid"}`

## Pattern 10: Hierarchical Rules

**Pattern**: Category → Subcategory → Specific rules

**Strategy**: Build belief hierarchy through chained implications

```json
{
  "beliefs": [
    {
      "scenario": {
        "type": "IF_THEN",
        "antecedents": [[{"valence": true, "sentence": "entity is a car"}]],
        "consequences": [{"modal": "Always", "properties": [{"valence": true, "sentence": "entity is a vehicle"}]}]

      },
      "beliefUniqueId": "belief-001",
      ...
    },
    {
      "scenario": {
        "type": "IF_THEN",
        "antecedents": [[{"valence": true, "sentence": "entity is a vehicle"}]],
        "consequences": [{"modal": "Always", "properties": [{"valence": true, "sentence": "entity requires fuel"}]}]

      },
      "beliefUniqueId": "belief-002",
      ...
    }
  ]
}
```

This creates: car → vehicle → requires fuel

## Edge Cases

### Universal/Unconditional Rules

**IMPORTANT**: Do NOT use empty antecedents `[[]]`. Every belief must have at least one antecedent property.

For universal rules that are always true (unconditional facts), use a universal context antecedent:

```json
{
  "antecedents": [
    [
      {
        "valence": true,
        "sentence": "this is the current context"
      }
    ]
  ]
}
```

Examples of universal antecedents:

- "this is the current context"
- "we are in scenario X"
- "this is a valid state"
- Any appropriate contextual statement that's always assumed true

### Complex Mixed Logic

"If (X AND Y) OR (Z AND W), then outcome"

Strategy: Split into two separate beliefs:

1. Belief 1: If X AND Y, then outcome
2. Belief 2: If Z AND W, then outcome

### Temporal Logic

"After X happens, then Y"

Strategy: Represent as standard IF_THEN:

- Antecedent: "X has happened"
- Consequence: "Y occurs"

## Anti-Patterns to Avoid

❌ **NEVER use empty antecedents**:

- Bad: `"antecedents": [[]]`
- Good: Use a universal context antecedent like `"this is the current context"`
- Every belief MUST have at least one antecedent property

❌ **Don't nest logic within sentences**:

- Bad: "If user is admin or owner, allow access"
- Good: Two separate beliefs for admin and owner

❌ **Don't mix positive and negative unnecessarily**:

- Instead of: "user is not inactive"
- Use: "user is active" with appropriate valence

❌ **Don't create circular dependencies**:

- Avoid: X → Y and Y → X (unless truly mutual inclusion)

❌ **Don't encode multiple unrelated rules in one belief**:

- Split them into separate beliefs with unique IDs
