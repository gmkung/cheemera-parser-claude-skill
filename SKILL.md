---
name: cheemera-parser
description: Parse any body of text, statements, rules, or principles into a Cheemera belief set in JSON format compatible with the deCheem inference engine. Use when the user asks to convert text, rules, principles, policies, or logical statements into Cheemera format, or when working with belief systems that need to be formalized for logical reasoning and deduction.
---

# Cheemera Belief Set Parser

Parse natural language statements, rules, principles, and logical relationships into structured Cheemera belief sets that are compatible with the deCheem inference engine.

## Overview

Cheemera belief sets represent systems of thought and beliefs in a structured JSON format. The deCheem inference engine uses these belief sets to perform both explicit and implicit logical deductions across rules written in human language.

## Core Concepts

### Belief Set Structure

A **Belief Set** is a JSON object containing:
- `beliefs`: Array of Belief objects representing rules and logical relationships

### Belief Structure

Each **Belief** represents a logical rule with:
- `Antecedents`: Array of strings representing conditions (the "IF" statements)
- `Consequences`: Array of strings representing outcomes (the "THEN" statements)

**Key principle**: Both antecedents and consequences are written in plain human language as strings. The engine does not depend on specific data types or predefined structures—anything that can be expressed as a string can be a belief.

## JSON Format

```json
{
  "beliefs": [
    {
      "Antecedents": [
        "condition 1",
        "condition 2"
      ],
      "Consequences": [
        "outcome 1",
        "outcome 2"
      ]
    }
  ]
}
```

## Parsing Guidelines

### 1. Identify Logical Relationships

Look for:
- If-then statements
- Cause-effect relationships  
- Rules and policies
- Principles and constraints
- Conditional logic
- Requirements and implications

### 2. Extract Antecedents (Conditions)

Antecedents represent the "IF" part:
- Preconditions that must be true
- Starting assumptions
- Prerequisites
- Given conditions
- Trigger events

**Multiple antecedents**: When multiple conditions are listed together, they form a compound condition (logical AND).

### 3. Extract Consequences (Outcomes)

Consequences represent the "THEN" part:
- Outcomes that follow
- Resulting states
- Conclusions
- Actions to take
- Derived facts

**Multiple consequences**: When multiple outcomes follow from the same conditions, list all consequences.

### 4. Atomic Decomposition

Break down complex statements into atomic beliefs:
- Each belief should represent a single logical relationship
- Avoid nesting complex logic within a single belief
- Split compound statements into multiple beliefs when appropriate

### 5. Natural Language Preservation

- Keep statements in clear, natural human language
- Preserve domain-specific terminology
- Maintain semantic meaning from the original text
- Use consistent phrasing across related beliefs

## Common Patterns

### Simple If-Then

**Input**: "If it rains, the ground gets wet"

**Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": ["it rains"],
      "Consequences": ["the ground gets wet"]
    }
  ]
}
```

### Multiple Conditions (AND)

**Input**: "If A and B are true, then C must be true"

**Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": ["A is true", "B is true"],
      "Consequences": ["C is true"]
    }
  ]
}
```

### Multiple Consequences

**Input**: "If the user clicks submit, validate the form and send the data to the server"

**Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": ["the user clicks submit"],
      "Consequences": [
        "validate the form",
        "send the data to the server"
      ]
    }
  ]
}
```

### Policy Rules

**Input**: "Users must be authenticated to access protected resources. Authenticated users have a valid session token."

**Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": ["user wants to access protected resources"],
      "Consequences": ["user must be authenticated"]
    },
    {
      "Antecedents": ["user is authenticated"],
      "Consequences": ["user has a valid session token"]
    }
  ]
}
```

### Negation and Constraints

**Input**: "If the balance is below zero, the account is overdrawn and transactions are blocked"

**Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": ["balance is below zero"],
      "Consequences": [
        "account is overdrawn",
        "transactions are blocked"
      ]
    }
  ]
}
```

## Workflow

When parsing text into Cheemera format:

1. **Read and understand** the source text thoroughly
2. **Identify all logical relationships** (if-then, cause-effect, rules)
3. **Extract antecedents** (conditions) for each relationship
4. **Extract consequences** (outcomes) for each relationship  
5. **Decompose complex statements** into atomic beliefs
6. **Formulate each belief** with clear antecedents and consequences arrays
7. **Assemble the belief set** JSON with all beliefs
8. **Validate** that the JSON is properly formatted
9. **Review** to ensure semantic meaning is preserved

## Advanced Features

### Domain-Agnostic

The format works across any domain:
- Legal reasoning
- Business rules
- Scientific principles
- Engineering constraints
- Philosophical arguments
- Software requirements

### Implicit Deduction Support

The deCheem engine can make implicit deductions. For example:
- Rule: "If A and B, then C"
- Given: C is false and A is true
- Deduction: B cannot be true

This means you don't need to explicitly state all possible logical combinations—the engine will derive them.

### Rule-Sequence Agnostic

Beliefs can be listed in any order. The engine will determine the correct logical relationships regardless of how beliefs are ordered in the array.

## Quality Checklist

- [ ] All beliefs have both Antecedents and Consequences arrays
- [ ] Each array contains at least one string element
- [ ] Statements are in clear, natural language
- [ ] Complex rules are decomposed into atomic beliefs
- [ ] JSON is valid and properly formatted
- [ ] Semantic meaning from original text is preserved
- [ ] Domain terminology is retained accurately
- [ ] Related beliefs use consistent phrasing

## Output Format

Always output a single JSON object with this exact structure:

```json
{
  "beliefs": [
    {
      "Antecedents": ["condition"],
      "Consequences": ["outcome"]
    }
  ]
}
```

Ensure the JSON is:
- Valid and parseable
- Properly formatted with correct syntax
- Uses double quotes for strings
- Has proper array and object structure

## Additional Resources

For more examples and patterns, see:
- `references/examples.md` - Extended examples across different domains
- `references/patterns.md` - Common parsing patterns and edge cases
