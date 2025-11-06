# Common Parsing Patterns and Edge Cases

Guide for handling various statement types and edge cases when parsing into Cheemera belief sets.

## Pattern: Implicit Conditionals

When conditions are implied but not explicitly stated.

**Input**: "Authenticated users can view their profile."

**Analysis**: The implicit condition is "user wants to view their profile"

**Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": [
        "user is authenticated",
        "user wants to view their profile"
      ],
      "Consequences": ["user can view their profile"]
    }
  ]
}
```

## Pattern: Universal Statements

Statements that apply to all instances.

**Input**: "All employees must complete security training."

**Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": ["person is an employee"],
      "Consequences": ["person must complete security training"]
    }
  ]
}
```

## Pattern: Negative Conditions

Handling negation in statements.

**Input**: "If the user is not logged in, redirect to login page."

**Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": ["user is not logged in"],
      "Consequences": ["redirect user to login page"]
    }
  ]
}
```

## Pattern: Disjunctions (OR Conditions)

When multiple alternative conditions can lead to the same outcome.

**Input**: "Premium features are available if the user has a paid subscription or is in a free trial period."

**Approach**: Create separate beliefs for each alternative.

**Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": ["user has paid subscription"],
      "Consequences": ["premium features are available to user"]
    },
    {
      "Antecedents": ["user is in free trial period"],
      "Consequences": ["premium features are available to user"]
    }
  ]
}
```

## Pattern: Temporal Conditions

Statements involving time-based logic.

**Input**: "After three failed attempts, the system locks the account for 15 minutes."

**Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": ["there have been three failed login attempts"],
      "Consequences": [
        "system locks the account",
        "account is locked for 15 minutes"
      ]
    }
  ]
}
```

## Pattern: Hierarchical Relationships

When outcomes depend on a chain of conditions.

**Input**: "To deploy to production, code must pass all tests. Tests can only run if the build succeeds. The build requires all dependencies to be installed."

**Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": ["all dependencies are installed"],
      "Consequences": ["build can be executed"]
    },
    {
      "Antecedents": ["build succeeds"],
      "Consequences": ["tests can run"]
    },
    {
      "Antecedents": ["all tests pass"],
      "Consequences": ["code can be deployed to production"]
    }
  ]
}
```

## Pattern: State Transitions

Describing changes in state.

**Input**: "When the order is confirmed, its status changes from pending to processing. After payment is received, status changes from processing to completed."

**Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": [
        "order is confirmed",
        "order status is pending"
      ],
      "Consequences": ["order status changes to processing"]
    },
    {
      "Antecedents": [
        "payment is received",
        "order status is processing"
      ],
      "Consequences": ["order status changes to completed"]
    }
  ]
}
```

## Pattern: Quantitative Thresholds

Handling numeric comparisons and ranges.

**Input**: "Items weighing over 50 pounds require special handling. Items between 20-50 pounds need standard packaging. Items under 20 pounds can use economy packaging."

**Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": ["item weighs over 50 pounds"],
      "Consequences": ["item requires special handling"]
    },
    {
      "Antecedents": [
        "item weighs 20 pounds or more",
        "item weighs 50 pounds or less"
      ],
      "Consequences": ["item needs standard packaging"]
    },
    {
      "Antecedents": ["item weighs under 20 pounds"],
      "Consequences": ["item can use economy packaging"]
    }
  ]
}
```

## Pattern: Exception Handling

Statements that define exceptions to rules.

**Input**: "All purchases require payment, except for promotional items which are free."

**Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": [
        "item is purchased",
        "item is not promotional"
      ],
      "Consequences": ["payment is required"]
    },
    {
      "Antecedents": ["item is promotional"],
      "Consequences": [
        "item is free",
        "payment is not required"
      ]
    }
  ]
}
```

## Pattern: Reciprocal Relationships

When relationships work in both directions.

**Input**: "Project managers can assign tasks to team members. Team members can request tasks from project managers."

**Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": [
        "user is a project manager",
        "recipient is a team member"
      ],
      "Consequences": ["user can assign tasks to recipient"]
    },
    {
      "Antecedents": [
        "user is a team member",
        "recipient is a project manager"
      ],
      "Consequences": ["user can request tasks from recipient"]
    }
  ]
}
```

## Pattern: Prerequisite Chains

Multiple prerequisites that must be satisfied in order.

**Input**: "Before submitting an article, it must be reviewed. Before review, it must be written. Before writing, a topic must be approved."

**Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": ["topic is approved"],
      "Consequences": ["article can be written"]
    },
    {
      "Antecedents": ["article is written"],
      "Consequences": ["article can be reviewed"]
    },
    {
      "Antecedents": ["article is reviewed"],
      "Consequences": ["article can be submitted"]
    }
  ]
}
```

## Pattern: Permission and Capability

Distinguishing between permission and ability.

**Input**: "Users can edit their own posts. Moderators can edit any post."

**Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": [
        "user wants to edit a post",
        "user is the author of the post"
      ],
      "Consequences": ["user can edit the post"]
    },
    {
      "Antecedents": [
        "user is a moderator",
        "there is a post to edit"
      ],
      "Consequences": ["user can edit the post"]
    }
  ]
}
```

## Edge Case: Tautologies

Statements that are always true should still be captured.

**Input**: "All valid emails must contain an @ symbol."

**Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": ["email is valid"],
      "Consequences": ["email contains @ symbol"]
    }
  ]
}
```

## Edge Case: Contradictions

If source text contains contradictions, capture both as separate beliefs.

**Input**: "Standard users cannot delete posts. Standard users can delete their own posts."

**Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": [
        "user is a standard user",
        "post is not owned by user"
      ],
      "Consequences": ["user cannot delete the post"]
    },
    {
      "Antecedents": [
        "user is a standard user",
        "post is owned by user"
      ],
      "Consequences": ["user can delete the post"]
    }
  ]
}
```

## Edge Case: Ambiguous Pronouns

Clarify pronouns in the parsed output.

**Input**: "When a customer places an order, send them a confirmation email."

**Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": ["customer places an order"],
      "Consequences": ["send confirmation email to the customer"]
    }
  ]
}
```

## Edge Case: Lists and Enumerations

Handle lists consistently.

**Input**: "Valid payment methods include credit card, PayPal, and bank transfer."

**Approach**: Create beliefs for each item or group them based on context.

**Output (Option 1 - Atomic)**:
```json
{
  "beliefs": [
    {
      "Antecedents": ["payment method is credit card"],
      "Consequences": ["payment method is valid"]
    },
    {
      "Antecedents": ["payment method is PayPal"],
      "Consequences": ["payment method is valid"]
    },
    {
      "Antecedents": ["payment method is bank transfer"],
      "Consequences": ["payment method is valid"]
    }
  ]
}
```

**Output (Option 2 - Grouped)**:
```json
{
  "beliefs": [
    {
      "Antecedents": [
        "payment method is one of: credit card, PayPal, or bank transfer"
      ],
      "Consequences": ["payment method is valid"]
    }
  ]
}
```

## Best Practice: Consistency

When parsing related statements, maintain consistent phrasing:

**Good**:
- "user is authenticated" (used throughout)
- "user has valid credentials" (used throughout)

**Avoid**:
- Mixing "user is authenticated" and "the user has been authenticated" and "authentication successful"

## Best Practice: Granularity

Choose appropriate level of detail:

**Too Granular**: Breaking "if A and B then C" into separate beliefs for every possible combination
**Too Coarse**: Combining unrelated rules into single complex belief
**Just Right**: Each belief represents one logical relationship clearly
