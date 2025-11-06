# Cheemera Belief Set Examples

Examples of parsing text into Cheemera belief sets using the correct server-compatible format.

## Format Reference

All examples follow this structure:
- Field order: `scenario` → `beliefUniqueId` → `originatingRuleSystemName` → `originatingRuleSystemUuid`
- In scenario: `type` → `consequences` → `antecedents`
- In properties: `valence` → `sentence`
- In consequences: `modal` → `properties`

## Example 1: Simple Business Rule

**Input**: "If a customer has a premium account, they get free shipping."

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
                "sentence": "the customer gets free shipping"
              }
            ]
          }
        ],
        "antecedents": [
          [
            {
              "valence": true,
              "sentence": "the customer has a premium account"
            }
          ]
        ]
      },
      "beliefUniqueId": "belief-001",
      "originatingRuleSystemName": "E-commerce Rules",
      "originatingRuleSystemUuid": "uuid-ecommerce"
    }
  ],
  "beliefSetName": "E-commerce Rules",
  "beliefSetOwner": "System",
  "beliefSetVersion": "1.0",
  "blindReferenceExternalIdArray": []
}
```

## Example 2: Multiple Conditions (AND Logic)

**Input**: "If a user is authenticated AND has admin privileges, they can access the admin panel."

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
                "sentence": "the user can access the admin panel"
              }
            ]
          }
        ],
        "antecedents": [
          [
            {
              "valence": true,
              "sentence": "the user is authenticated"
            },
            {
              "valence": true,
              "sentence": "the user has admin privileges"
            }
          ]
        ]
      },
      "beliefUniqueId": "belief-001",
      "originatingRuleSystemName": "Access Control",
      "originatingRuleSystemUuid": "uuid-access"
    }
  ],
  "beliefSetName": "Access Control Rules",
  "beliefSetOwner": "Security Team",
  "beliefSetVersion": "1.0",
  "blindReferenceExternalIdArray": []
}
```

## Example 3: Multiple Consequences

**Input**: "If payment is successful, send confirmation email and update order status."

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
                "sentence": "send confirmation email"
              },
              {
                "valence": true,
                "sentence": "update order status"
              }
            ]
          }
        ],
        "antecedents": [
          [
            {
              "valence": true,
              "sentence": "payment is successful"
            }
          ]
        ]
      },
      "beliefUniqueId": "belief-001",
      "originatingRuleSystemName": "Payment Processing",
      "originatingRuleSystemUuid": "uuid-payment"
    }
  ],
  "beliefSetName": "Payment Processing Rules",
  "beliefSetOwner": "Payment Team",
  "beliefSetVersion": "1.0",
  "blindReferenceExternalIdArray": []
}
```

## Example 4: Negation (Using valence: false)

**Input**: "If the user is not verified, they cannot make purchases."

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
                "valence": false,
                "sentence": "the user can make purchases"
              }
            ]
          }
        ],
        "antecedents": [
          [
            {
              "valence": false,
              "sentence": "the user is verified"
            }
          ]
        ]
      },
      "beliefUniqueId": "belief-001",
      "originatingRuleSystemName": "User Verification",
      "originatingRuleSystemUuid": "uuid-verification"
    }
  ],
  "beliefSetName": "User Verification Rules",
  "beliefSetOwner": "Compliance Team",
  "beliefSetVersion": "1.0",
  "blindReferenceExternalIdArray": []
}
```

## Example 5: Multiple Beliefs in One Set

**Input**:
"Users must be 18 or older to register. Registered users can post comments. Users under 18 cannot register."

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
                "sentence": "the user can register"
              }
            ]
          }
        ],
        "antecedents": [
          [
            {
              "valence": true,
              "sentence": "the user is 18 or older"
            }
          ]
        ]
      },
      "beliefUniqueId": "belief-001",
      "originatingRuleSystemName": "Age Verification",
      "originatingRuleSystemUuid": "uuid-age"
    },
    {
      "scenario": {
        "type": "IF_THEN",
        "consequences": [
          {
            "modal": "Always",
            "properties": [
              {
                "valence": true,
                "sentence": "the user can post comments"
              }
            ]
          }
        ],
        "antecedents": [
          [
            {
              "valence": true,
              "sentence": "the user is registered"
            }
          ]
        ]
      },
      "beliefUniqueId": "belief-002",
      "originatingRuleSystemName": "Age Verification",
      "originatingRuleSystemUuid": "uuid-age"
    },
    {
      "scenario": {
        "type": "IF_THEN",
        "consequences": [
          {
            "modal": "Always",
            "properties": [
              {
                "valence": false,
                "sentence": "the user can register"
              }
            ]
          }
        ],
        "antecedents": [
          [
            {
              "valence": true,
              "sentence": "the user is under 18"
            }
          ]
        ]
      },
      "beliefUniqueId": "belief-003",
      "originatingRuleSystemName": "Age Verification",
      "originatingRuleSystemUuid": "uuid-age"
    }
  ],
  "beliefSetName": "Age Verification Rules",
  "beliefSetOwner": "Legal Team",
  "beliefSetVersion": "1.0",
  "blindReferenceExternalIdArray": []
}
```

## Example 6: Mutual Exclusion

**Input**: "A transaction cannot be both completed and pending."

**Output**:
```json
{
  "beliefs": [
    {
      "scenario": {
        "type": "MUTUAL_EXCLUSION",
        "consequences": [
          {
            "modal": "Never",
            "properties": [
              {
                "valence": true,
                "sentence": "the transaction is completed"
              },
              {
                "valence": true,
                "sentence": "the transaction is pending"
              }
            ]
          }
        ],
        "antecedents": [[]]
      },
      "beliefUniqueId": "belief-001",
      "originatingRuleSystemName": "Transaction States",
      "originatingRuleSystemUuid": "uuid-transactions"
    }
  ],
  "beliefSetName": "Transaction State Rules",
  "beliefSetOwner": "System",
  "beliefSetVersion": "1.0",
  "blindReferenceExternalIdArray": []
}
```

## Tips for Creating Belief Sets

1. **Unique IDs**: Use sequential IDs like "belief-001", "belief-002", or generate UUIDs
2. **System UUID**: Use the same UUID for all beliefs in a set that come from the same rule system
3. **Valence**:
   - `true` for positive statements ("user is verified")
   - `false` for negative statements ("user is not verified")
4. **Modal**:
   - `"Always"` for IF_THEN rules (if X then Y always happens)
   - `"Never"` for MUTUAL_EXCLUSION (X and Y never both true)
5. **Scenario Types**:
   - `"IF_THEN"` - Standard conditional rules
   - `"MUTUAL_EXCLUSION"` - Things that cannot coexist
   - `"MUTUAL_INCLUSION"` - Things that must coexist
