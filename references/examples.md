# Cheemera Belief Set Examples

Comprehensive examples of parsing text into Cheemera belief sets across different domains.

## Legal Reasoning

### Contract Law Example

**Input Text**:
"A valid contract requires an offer, acceptance, and consideration. If all three elements are present, the contract is legally binding. Minors cannot enter into legally binding contracts."

**Cheemera Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": [
        "there is an offer",
        "there is acceptance",
        "there is consideration"
      ],
      "Consequences": [
        "there is a valid contract"
      ]
    },
    {
      "Antecedents": [
        "there is a valid contract"
      ],
      "Consequences": [
        "the contract is legally binding"
      ]
    },
    {
      "Antecedents": [
        "the party is a minor"
      ],
      "Consequences": [
        "the party cannot enter into legally binding contracts"
      ]
    }
  ]
}
```

## Business Rules

### E-commerce Example

**Input Text**:
"Free shipping applies to orders over $50. If a customer has a premium membership, they get free shipping on all orders. Orders placed before 2 PM are shipped same day. Refunds are only available within 30 days of purchase."

**Cheemera Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": ["order total is over $50"],
      "Consequences": ["free shipping applies"]
    },
    {
      "Antecedents": ["customer has premium membership"],
      "Consequences": ["free shipping applies to the order"]
    },
    {
      "Antecedents": ["order is placed before 2 PM"],
      "Consequences": ["order is shipped same day"]
    },
    {
      "Antecedents": [
        "refund is requested",
        "purchase was within the last 30 days"
      ],
      "Consequences": ["refund is available"]
    }
  ]
}
```

## Software Engineering

### Authentication System

**Input Text**:
"When a user logs in, verify their credentials against the database. If credentials are valid, create a session token and store it in the cache. If the session token exists and is not expired, the user is authenticated. Failed login attempts exceeding 5 times lock the account for 30 minutes."

**Cheemera Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": ["user logs in"],
      "Consequences": ["verify credentials against database"]
    },
    {
      "Antecedents": ["credentials are valid"],
      "Consequences": [
        "create a session token",
        "store session token in cache"
      ]
    },
    {
      "Antecedents": [
        "session token exists",
        "session token is not expired"
      ],
      "Consequences": ["user is authenticated"]
    },
    {
      "Antecedents": ["failed login attempts exceed 5"],
      "Consequences": [
        "account is locked",
        "account remains locked for 30 minutes"
      ]
    }
  ]
}
```

## Scientific Principles

### Physics Example

**Input Text**:
"Objects at rest remain at rest unless acted upon by an external force. Objects in motion continue in uniform motion unless acted upon by an external force. The acceleration of an object is proportional to the net force acting on it and inversely proportional to its mass."

**Cheemera Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": [
        "object is at rest",
        "no external force acts on the object"
      ],
      "Consequences": ["object remains at rest"]
    },
    {
      "Antecedents": [
        "object is in motion",
        "no external force acts on the object"
      ],
      "Consequences": ["object continues in uniform motion"]
    },
    {
      "Antecedents": [
        "net force acts on object",
        "object has mass"
      ],
      "Consequences": [
        "object accelerates",
        "acceleration is proportional to net force",
        "acceleration is inversely proportional to mass"
      ]
    }
  ]
}
```

## Healthcare Policy

### Medical Records Example

**Input Text**:
"Patient consent is required to share medical records with third parties. Emergency situations allow access to medical records without consent. Healthcare providers must document all access to patient records. Records must be retained for a minimum of 7 years."

**Cheemera Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": [
        "medical records need to be shared with third party",
        "not an emergency situation"
      ],
      "Consequences": ["patient consent is required"]
    },
    {
      "Antecedents": ["situation is an emergency"],
      "Consequences": ["medical records can be accessed without consent"]
    },
    {
      "Antecedents": ["patient record is accessed"],
      "Consequences": ["healthcare provider must document the access"]
    },
    {
      "Antecedents": ["medical record exists"],
      "Consequences": ["record must be retained for minimum 7 years"]
    }
  ]
}
```

## Game Logic

### RPG Mechanics Example

**Input Text**:
"Players with strength above 15 can equip heavy armor. Heavy armor reduces agility by 3 points. If agility drops below 5, the player cannot dodge attacks. Critical hits occur when attack roll is 18 or higher and the target is surprised."

**Cheemera Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": ["player strength is above 15"],
      "Consequences": ["player can equip heavy armor"]
    },
    {
      "Antecedents": ["player equips heavy armor"],
      "Consequences": ["player agility is reduced by 3 points"]
    },
    {
      "Antecedents": ["player agility is below 5"],
      "Consequences": ["player cannot dodge attacks"]
    },
    {
      "Antecedents": [
        "attack roll is 18 or higher",
        "target is surprised"
      ],
      "Consequences": ["attack is a critical hit"]
    }
  ]
}
```

## Environmental Rules

### Climate Control Example

**Input Text**:
"When temperature exceeds 75°F, activate cooling system. If humidity is above 60%, increase ventilation. When both temperature is high and humidity is high, activate dehumidifier before cooling. Energy saving mode disables cooling when no occupancy is detected for 30 minutes."

**Cheemera Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": ["temperature exceeds 75°F"],
      "Consequences": ["activate cooling system"]
    },
    {
      "Antecedents": ["humidity is above 60%"],
      "Consequences": ["increase ventilation"]
    },
    {
      "Antecedents": [
        "temperature is high",
        "humidity is high"
      ],
      "Consequences": [
        "activate dehumidifier",
        "dehumidifier activates before cooling"
      ]
    },
    {
      "Antecedents": [
        "energy saving mode is enabled",
        "no occupancy detected for 30 minutes"
      ],
      "Consequences": ["disable cooling system"]
    }
  ]
}
```

## Financial Rules

### Investment Portfolio Example

**Input Text**:
"High-risk investments should not exceed 20% of total portfolio for conservative investors. Bonds are considered low-risk investments. If market volatility index exceeds 30, reduce equity exposure by 10%. Rebalancing is required when any asset class deviates more than 5% from target allocation."

**Cheemera Output**:
```json
{
  "beliefs": [
    {
      "Antecedents": [
        "investor is conservative",
        "investment is high-risk"
      ],
      "Consequences": ["investment should not exceed 20% of portfolio"]
    },
    {
      "Antecedents": ["investment is a bond"],
      "Consequences": ["investment is low-risk"]
    },
    {
      "Antecedents": ["market volatility index exceeds 30"],
      "Consequences": ["reduce equity exposure by 10%"]
    },
    {
      "Antecedents": ["asset class deviates more than 5% from target allocation"],
      "Consequences": ["portfolio rebalancing is required"]
    }
  ]
}
```
