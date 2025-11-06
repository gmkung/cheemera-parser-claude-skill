# Common Parsing Patterns

Patterns and strategies for converting different types of statements into Cheemera belief sets.

## Pattern 1: Simple If-Then

**Pattern**: "If X, then Y"

**Structure**:
```json
{
  "scenario": {
    "type": "IF_THEN",
    "consequences": [{ "modal": "Always", "properties": [{"valence": true, "sentence": "Y"}] }],
    "antecedents": [[{"valence": true, "sentence": "X"}]]
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
      {"valence": true, "sentence": "X"},
      {"valence": true, "sentence": "Y"},
      {"valence": true, "sentence": "Z"}
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
        "consequences": [{"modal": "Always", "properties": [{"valence": true, "sentence": "outcome"}]}],
        "antecedents": [[{"valence": true, "sentence": "X"}]]
      },
      "beliefUniqueId": "belief-001",
      ...
    },
    {
      "scenario": {
        "type": "IF_THEN",
        "consequences": [{"modal": "Always", "properties": [{"valence": true, "sentence": "outcome"}]}],
        "antecedents": [[{"valence": true, "sentence": "Y"}]]
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
  "antecedents": [[{"valence": false, "sentence": "X"}]]
}
```

**Consequence negation**:
```json
{
  "consequences": [{"modal": "Always", "properties": [{"valence": false, "sentence": "Y"}]}]
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
        {"valence": true, "sentence": "Y"},
        {"valence": true, "sentence": "Z"},
        {"valence": true, "sentence": "W"}
      ]
    }
  ]
}
```

**Example**: "If order confirmed, send email AND update inventory AND charge payment"

## Pattern 6: Mutual Exclusion

**Pattern**: "X and Y cannot both be true"

**Structure**: Use `MUTUAL_EXCLUSION` type with `Never` modal
```json
{
  "scenario": {
    "type": "MUTUAL_EXCLUSION",
    "consequences": [
      {
        "modal": "Never",
        "properties": [
          {"valence": true, "sentence": "X"},
          {"valence": true, "sentence": "Y"}
        ]
      }
    ],
    "antecedents": [[]]
  }
}
```

**Examples**:
- "A door cannot be both open and closed"
- "A user cannot be both active and deleted"
- "Transaction cannot be both pending and completed"

## Pattern 7: Mutual Inclusion

**Pattern**: "If X is true, Y must also be true" (bidirectional dependency)

**Structure**: Use `MUTUAL_INCLUSION` type with `Always` modal
```json
{
  "scenario": {
    "type": "MUTUAL_INCLUSION",
    "consequences": [
      {
        "modal": "Always",
        "properties": [
          {"valence": true, "sentence": "X"},
          {"valence": true, "sentence": "Y"}
        ]
      }
    ],
    "antecedents": [[]]
  }
}
```

**Examples**:
- "If a user has premium features, they must have an active subscription"
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
        "consequences": [{"modal": "Always", "properties": [{"valence": true, "sentence": "Y"}]}],
        "antecedents": [[{"valence": true, "sentence": "X"}]]
      },
      "beliefUniqueId": "belief-001",
      ...
    },
    {
      "scenario": {
        "type": "IF_THEN",
        "consequences": [{"modal": "Always", "properties": [{"valence": true, "sentence": "Z"}]}],
        "antecedents": [[{"valence": true, "sentence": "Y"}]]
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
    "consequences": [
      {
        "modal": "Always",
        "properties": [
          {"valence": true, "sentence": "the constraint is violated"}
        ]
      }
    ],
    "antecedents": [[
      {"valence": true, "sentence": "invalid condition"}
    ]]
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
        "consequences": [{"modal": "Always", "properties": [{"valence": true, "sentence": "entity is a vehicle"}]}],
        "antecedents": [[{"valence": true, "sentence": "entity is a car"}]]
      },
      "beliefUniqueId": "belief-001",
      ...
    },
    {
      "scenario": {
        "type": "IF_THEN",
        "consequences": [{"modal": "Always", "properties": [{"valence": true, "sentence": "entity requires fuel"}]}],
        "antecedents": [[{"valence": true, "sentence": "entity is a vehicle"}]]
      },
      "beliefUniqueId": "belief-002",
      ...
    }
  ]
}
```

This creates: car → vehicle → requires fuel

## Edge Cases

### Empty Antecedents
For universal rules with no conditions, use empty antecedent array:
```json
{
  "antecedents": [[]]
}
```

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
