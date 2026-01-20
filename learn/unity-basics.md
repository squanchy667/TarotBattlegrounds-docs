# Unity 2023 LTS Basics

## Unity Interface

- **Scene View**: Visual editor for game objects
- **Game View**: Play mode preview
- **Hierarchy**: Game object tree
- **Inspector**: Component properties
- **Project**: Asset browser
- **Console**: Debug logs

---

## Key Concepts

### GameObjects
- Everything is a GameObject
- GameObjects have Components
- Transform component = position/rotation/scale

### MonoBehaviour
```csharp
public class Example : MonoBehaviour
{
    void Start() { }    // Called once on start
    void Update() { }   // Called every frame
}
```

### ScriptableObjects
```csharp
[CreateAssetMenu]
public class Card : ScriptableObject
{
    public int attack;
    public int health;
}
```

---

## Common Operations

### Instantiate Object
```csharp
GameObject obj = Instantiate(prefab, position, rotation);
```

### Find GameObject
```csharp
GameObject obj = GameObject.Find("ObjectName");
```

### Get Component
```csharp
Rigidbody rb = GetComponent<Rigidbody>();
```

---

## Debugging

```csharp
Debug.Log("Hello World");
Debug.LogWarning("Warning!");
Debug.LogError("Error!");
```

---

## Resources

- [Unity Manual](https://docs.unity3d.com/Manual/index.html)
- [Unity Scripting API](https://docs.unity3d.com/ScriptReference/)
