# JSON-сериализация

JSON — стандартный способ сохранять сложные структуры данных (инвентарь, прогресс, настройки). Unity предоставляет встроенный `JsonUtility`, а для более сложных задач используется `Newtonsoft.Json` (Json.NET).

## JsonUtility — встроенный вариант

### Подготовка класса данных

```csharp
using System;
using System.Collections.Generic;

[Serializable]
public class PlayerData
{
    public string   playerName;
    public int      level;
    public float    health;
    public Vector3  position;
    public List<string> inventory = new List<string>();
}
```

> Все поля должны быть `public` **или** иметь атрибут `[SerializeField]`. Класс помечается `[Serializable]`.

### Сериализация (объект → JSON)

```csharp
PlayerData data = new PlayerData
{
    playerName = "Иван",
    level      = 5,
    health     = 80f,
    position   = new Vector3(1f, 0f, 3f),
    inventory  = new List<string> { "Меч", "Зелье" }
};

string json = JsonUtility.ToJson(data);
// {"playerName":"Иван","level":5,"health":80.0,"position":{"x":1.0,"y":0.0,"z":3.0},"inventory":["Меч","Зелье"]}

// Форматированный (читаемый) JSON
string prettyJson = JsonUtility.ToJson(data, prettyPrint: true);
```

### Десериализация (JSON → объект)

```csharp
string json = /* считанная строка */;
PlayerData loaded = JsonUtility.FromJson<PlayerData>(json);

Debug.Log(loaded.playerName); // "Иван"
```

### Десериализация поверх существующего объекта

```csharp
// Обновляет поля существующего объекта, не создавая новый
PlayerData existing = new PlayerData();
JsonUtility.FromJsonOverwrite(json, existing);
```

## Newtonsoft.Json (Json.NET) — расширенный вариант

Доступен в Unity через **Package Manager → com.unity.nuget.newtonsoft-json**.

```csharp
using Newtonsoft.Json;

// Поддерживает словари, nullable, private-поля, конвертеры
var data = new Dictionary<string, object>
{
    { "name", "Иван" },
    { "level", 5 },
    { "active", true }
};

string json = JsonConvert.SerializeObject(data, Formatting.Indented);
var loaded  = JsonConvert.DeserializeObject<Dictionary<string, object>>(json);
```

### Когда использовать Newtonsoft вместо JsonUtility

| Возможность | JsonUtility | Newtonsoft |
|---|---|---|
| Словари (`Dictionary`) | ❌ | ✅ |
| Nullable типы | ❌ | ✅ |
| Перечисления как строки | ❌ | ✅ |
| Private поля | только `[SerializeField]` | `[JsonProperty]` |
| Полиморфизм / наследование | ❌ | ✅ |
| Производительность | ✅ Быстрее | Медленнее |
| Зависимость | встроен | нужен пакет |

## Сохранение enum как строки (Newtonsoft)

```csharp
using Newtonsoft.Json;
using Newtonsoft.Json.Converters;

[Serializable]
public class Settings
{
    [JsonConverter(typeof(StringEnumConverter))]
    public QualityLevel quality;
}

public enum QualityLevel { Low, Medium, High, Ultra }
```

## Рекомендации

- `JsonUtility` быстрее и не требует зависимостей — используйте для простых `[Serializable]`-классов.
- Для словарей, полиморфизма и сложных структур берите **Newtonsoft.Json**.
- Всегда оборачивайте `FromJson` / `DeserializeObject` в `try-catch` — файл может быть повреждён.
- Не сериализуйте `MonoBehaviour` или `GameObject` через JsonUtility напрямую — только чистые C#-классы.
