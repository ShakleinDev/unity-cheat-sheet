# Unity Scriptable Objects — Руководство по контейнерам данных

```csharp
// ScriptableObjects — это контейнеры данных, которые позволяют хранить большие объёмы данных независимо от экземпляров классов.
[CreateAssetMenu(fileName = "NewData", menuName = "ScriptableObjects/Data")]
public class Data : ScriptableObject {
    public string dataName;
    public int dataValue;
}

// Применение
Data myData = ScriptableObject.CreateInstance<Data>();
```

