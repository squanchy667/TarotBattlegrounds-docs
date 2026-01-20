# Coding Standards

## C# Style Guide

### Naming Conventions

```csharp
// PascalCase for classes, methods, properties
public class CardManager { }
public void BuyCard() { }
public int AttackPower { get; set; }

// camelCase for local variables, parameters
int cardCount = 0;
void SellCard(Card targetCard) { }

// _camelCase for private fields (optional)
private int _goldAmount;

// UPPER_CASE for constants
const int MAX_BOARD_SIZE = 7;
```

### Code Structure

```csharp
// Order: fields, properties, Unity methods, custom methods
public class Example : MonoBehaviour
{
    // 1. Fields
    private int value;
    
    // 2. Properties
    public int Value { get; set; }
    
    // 3. Unity Methods
    void Start() { }
    void Update() { }
    
    // 4. Custom Methods
    public void CustomMethod() { }
}
```

### Comments

```csharp
// Use // for single-line comments

/// <summary>
/// Use XML comments for public methods
/// </summary>
public void PublicMethod() { }

// TODO: For temporary code
// FIXME: For known issues
// NOTE: For important context
```

---

## Best Practices

### ✅ DO
- Use ScriptableObjects for data
- Separate concerns (Managers pattern)
- Write descriptive variable names
- Add debug logs for key events
- Use early returns to reduce nesting

### ❌ DON'T
- Hardcode values (use SerializeField)
- Use Update() for non-frame-dependent logic
- Create God classes (>500 lines)
- Ignore compiler warnings

---

## Unity-Specific

```csharp
// SerializeField for Inspector editing
[SerializeField] private int maxHealth;

// Header for organization
[Header("Combat Settings")]
public int damage;

// Tooltip for clarity
[Tooltip("Damage dealt per turn")]
public int attackPower;
```

---

## Git Commit Messages

```
feat: Add new card system
fix: Resolve gold calculation bug
docs: Update README with setup steps
refactor: Simplify combat logic
test: Add unit tests for TavernManager
```

---

## Related Documentation

- [Architecture](architecture.md)
- [Setup Guide](setup-guide.md)
