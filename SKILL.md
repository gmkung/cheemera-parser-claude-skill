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

Each **Belief** represents a logical rule with these fields IN THIS EXACT ORDER:
1. `scenario`: Object containing the logical structure
   - `type`: Scenario type - "IF_THEN", "MUTUAL_EXCLUSION", or "MUTUAL_INCLUSION"
   - `consequences`: Array of Consequence objects (outcomes) - MUST come before antecedents
   - `antecedents`: Array of arrays of Property objects (conditions)
2. `beliefUniqueId`: Unique identifier (UUID format recommended)
3. `originatingRuleSystemName`: Name of the source system or domain
4. `originatingRuleSystemUuid`: UUID of the originating rule system

**Property** structure (fields IN THIS EXACT ORDER):
1. `valence`: Boolean (true for positive, false for negative)
2. `sentence`: String representing the statement in natural language

**Consequence** structure (fields IN THIS EXACT ORDER):
1. `modal`: "Always" or "Never"
2. `properties`: Array of Property objects

**Key principle**: Statements are written in plain human language. The engine does not depend on specific data types—anything that can be expressed as a string can be a belief. FIELD ORDER MATTERS - always follow the exact order specified above.

## JSON Schema

The following JSON Schema defines the exact structure required:

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "required": ["beliefs", "beliefSetName", "beliefSetOwner", "beliefSetVersion", "blindReferenceExternalIdArray"],
  "properties": {
    "beliefs": {
      "type": "array",
      "items": {
        "type": "object",
        "required": ["scenario", "beliefUniqueId", "originatingRuleSystemName", "originatingRuleSystemUuid"],
        "properties": {
          "scenario": {
            "type": "object",
            "required": ["type", "consequences", "antecedents"],
            "properties": {
              "type": {
                "type": "string",
                "enum": ["IF_THEN", "MUTUAL_EXCLUSION", "MUTUAL_INCLUSION"]
              },
              "consequences": {
                "type": "array",
                "items": {
                  "type": "object",
                  "required": ["modal", "properties"],
                  "properties": {
                    "modal": {
                      "type": "string",
                      "enum": ["Always", "Never"]
                    },
                    "properties": {
                      "type": "array",
                      "items": {
                        "type": "object",
                        "required": ["valence", "sentence"],
                        "properties": {
                          "valence": { "type": "boolean" },
                          "sentence": { "type": "string" }
                        }
                      }
                    }
                  }
                }
              },
              "antecedents": {
                "type": "array",
                "items": {
                  "type": "array",
                  "items": {
                    "type": "object",
                    "required": ["valence", "sentence"],
                    "properties": {
                      "valence": { "type": "boolean" },
                      "sentence": { "type": "string" }
                    }
                  }
                }
              }
            }
          },
          "beliefUniqueId": { "type": "string" },
          "originatingRuleSystemName": { "type": "string" },
          "originatingRuleSystemUuid": { "type": "string" }
        }
      }
    },
    "beliefSetName": { "type": "string" },
    "beliefSetOwner": { "type": "string" },
    "beliefSetVersion": { "type": "string" },
    "blindReferenceExternalIdArray": { "type": "array" }
  }
}
```

## JSON Format Example

```json
{
  "beliefs": [
    {
      "scenario": {
        "type": "IF_THEN",
        "consequences": [
          {
            "modal": "Always",
            "properties": [
              {
                "valence": true,
                "sentence": "outcome 1"
              },
              {
                "valence": true,
                "sentence": "outcome 2"
              }
            ]
          }
        ],
        "antecedents": [
          [
            {
              "valence": true,
              "sentence": "condition 1"
            },
            {
              "valence": true,
              "sentence": "condition 2"
            }
          ]
        ]
      },
      "beliefUniqueId": "uuid-1",
      "originatingRuleSystemName": "System Name",
      "originatingRuleSystemUuid": "uuid-system"
    }
  ],
  "beliefSetName": "Belief Set Name",
  "beliefSetOwner": "Owner Name",
  "beliefSetVersion": "1.0",
  "blindReferenceExternalIdArray": []
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
      "scenario": {
        "type": "IF_THEN",
        "consequences": [
          {
            "modal": "Always",
            "properties": [
              {
                "valence": true,
                "sentence": "the ground gets wet"
              }
            ]
          }
        ],
        "antecedents": [
          [
            {
              "valence": true,
              "sentence": "it rains"
            }
          ]
        ]
      },
      "beliefUniqueId": "belief-001",
      "originatingRuleSystemName": "Weather Rules",
      "originatingRuleSystemUuid": "uuid-weather"
    }
  ],
  "beliefSetName": "Weather Rules",
  "beliefSetOwner": "System",
  "beliefSetVersion": "1.0",
  "blindReferenceExternalIdArray": []
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

Always output a single JSON object with this EXACT structure and field order:

```json
{
  "beliefs": [
    {
      "scenario": {
        "type": "IF_THEN",
        "consequences": [
          {
            "modal": "Always",
            "properties": [
              {
                "valence": true,
                "sentence": "outcome"
              }
            ]
          }
        ],
        "antecedents": [
          [
            {
              "valence": true,
              "sentence": "condition"
            }
          ]
        ]
      },
      "beliefUniqueId": "belief-001",
      "originatingRuleSystemName": "System Name",
      "originatingRuleSystemUuid": "uuid-system"
    }
  ],
  "beliefSetName": "Belief Set Name",
  "beliefSetOwner": "Owner Name",
  "beliefSetVersion": "1.0",
  "blindReferenceExternalIdArray": []
}
```

CRITICAL Requirements:
- Field order MUST match exactly as shown above
- "scenario" field comes FIRST in Belief object
- "consequences" comes BEFORE "antecedents" in scenario
- "valence" comes BEFORE "sentence" in Property objects
- "modal" comes BEFORE "properties" in Consequence objects
- Valid and parseable JSON
- Uses double quotes for strings
- Proper array and object structure

## Additional Resources

For more examples and patterns, see:
- `references/examples.md` - Extended examples across different domains
- `references/patterns.md` - Common parsing patterns and edge cases
