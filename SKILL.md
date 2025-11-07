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
   - `antecedents`: Array of arrays of Property objects (conditions)
   - `consequences`: Array of Consequence objects (outcomes) - should come after antecedents
2. `beliefUniqueId`: Unique identifier (UUID format recommended)
3. `originatingRuleSystemName`: Name of the source system or domain
4. `originatingRuleSystemUuid`: UUID of the originating rule system

**Property** structure (fields IN THIS EXACT ORDER):

1. `valence`: Boolean (true for positive, false for negative)
2. `sentence`: String representing the statement in natural language.

**Consequence** structure (fields IN THIS EXACT ORDER):

1. `modal`: "Always" or "Never"
2. `properties`: Array of Property objects

**Key principles**:

- Statements are written in plain human language. The engine does not depend on specific data types—anything that can be expressed as a string can be a belief
- FIELD ORDER MATTERS - always follow the exact order specified above
- **CRITICAL**: NEVER use empty antecedents `[[]]`. Every belief MUST have at least one antecedent property. For the rare unconditional/universal rules, use a contextual antecedent like `{"valence": true, "sentence": "this is the current context"}`

## JSON Schema

The following JSON Schema defines the exact structure required:

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "required": [
    "beliefs",
    "beliefSetName",
    "beliefSetOwner",
    "beliefSetVersion",
    "blindReferenceExternalIdArray"
  ],
  "properties": {
    "beliefs": {
      "type": "array",
      "items": {
        "type": "object",
        "required": [
          "scenario",
          "beliefUniqueId",
          "originatingRuleSystemName",
          "originatingRuleSystemUuid"
        ],
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
        ],
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

### Mutual Exclusion

**Input**: "A transaction cannot be both completed and pending."

**Important**: For MUTUAL_EXCLUSION, put the mutually exclusive properties in separate **antecedent arrays**, NOT in consequences. The server will automatically convert this into IF_THEN rules.

**Output**:

```json
{
  "beliefs": [
    {
      "scenario": {
        "type": "MUTUAL_EXCLUSION",
        "consequences": [],
        "antecedents": [
          [
            {
              "valence": true,
              "sentence": "the transaction is completed"
            }
          ],
          [
            {
              "valence": true,
              "sentence": "the transaction is pending"
            }
          ]
        ]
      },
      "beliefUniqueId": "belief-001",
      "originatingRuleSystemName": "Transaction Rules",
      "originatingRuleSystemUuid": "uuid-transactions"
    }
  ],
  "beliefSetName": "Transaction Rules",
  "beliefSetOwner": "System",
  "beliefSetVersion": "1.0",
  "blindReferenceExternalIdArray": []
}
```

**How it works**: The server converts this to:

- "If transaction is completed → transaction is NOT pending"
- "If transaction is pending → transaction is NOT completed"

### Mutual Inclusion

**Input**: "If a user has premium features, they must have an active subscription" (bidirectional requirement)

**Important**: For MUTUAL_INCLUSION, put the mutually inclusive properties in separate **antecedent arrays**, NOT in consequences.

**Output**:

```json
{
  "beliefs": [
    {
      "scenario": {
        "type": "MUTUAL_INCLUSION",
        "consequences": [],
        "antecedents": [
          [
            {
              "valence": true,
              "sentence": "user has premium features"
            }
          ],
          [
            {
              "valence": true,
              "sentence": "user has active subscription"
            }
          ]
        ]
      },
      "beliefUniqueId": "belief-001",
      "originatingRuleSystemName": "Subscription Rules",
      "originatingRuleSystemUuid": "uuid-subscription"
    }
  ],
  "beliefSetName": "Subscription Rules",
  "beliefSetOwner": "System",
  "beliefSetVersion": "1.0",
  "blindReferenceExternalIdArray": []
}
```

**How it works**: The server converts this to:

- "If user has premium features → user has active subscription"
- "If user has active subscription → user has premium features"

## Workflow

- When parsing text into the Cheemera format, remember that a good set of sentences is the bed rock of a good belief set.
- Like with proper English sentences, each sentence should start with a capital letter and ideally have the form of 'The {noun (phrase) in singular form} {verb} {adjective or description}.' Some examples
  1.  'The account is pending review.'
  2.  'The person has diabetes.'
  3.  'The weather will be rainy.'
  4.  'The piece of land is privately owned.'
- Each sentence in a good belief set should be used in as many beliefs as possible, but not more than necessary.
- **Maintain consistent noun phrase references**: Once you establish a noun phrase (e.g., "The entity is a robot"), use that same noun phrase consistently throughout related beliefs, and also in the adjectival phrases. Don't switch between "the robot", "the entity", "it" - always use the standardized reference established in your antecedents. Where possible, use the most generic word possible, and use a separate sentence to reflect what that generic word refers to (e.g. "The person accuses the entity" and "The entity is a robot").

- When generating a belief set:
  1. **Read and understand** First read the belief set in it's entirety,
  2. **Identify all logical relationships** (if-then, cause-effect, rules)
  3. Create the global sentence set:
     1. First list out all the unique applicable nouns (or noun phrases), followed by all possible 'positive' adjectives or adjectival phrases ('positive' e.g. 'has diabetes' instead of 'does not have diabetes'). Then deduplicate and merge the ones that actually mean the same thing. The goal is to standardize the vocabulary and phrasing uses in this belief set.
     2. After that use these noun (phrases) and adjectives to construct the beliefs, carefully using the standardized set of noun (phrases) and adjective (phrases) in these beliefs' antecedents and consequences.
  4. **Validate** that the JSON is properly formatted
  5. **Review** to ensure semantic meaning is preserved

## Additional Resources

For more examples and patterns, see:

- `references/examples.md` - Extended examples across different domains
- `references/patterns.md` - Common parsing patterns and edge cases
