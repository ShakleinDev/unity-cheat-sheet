# Передача данных между сценами

При загрузке новой сцены в режиме `Single` все объекты уничтожаются. Существует несколько подходов для сохранения данных между сценами.

## DontDestroyOnLoad

Объект с этим вызовом не уничтожается при смене сцены и переходит в специальную служебную сцену Unity.

```csharp
public class GameManager : MonoBehaviour
{
    public static GameManager Instance { get; private set; }

    public int  Score       { get; set; }
    public bool MusicOn     { get; set; } = true;

    void Awake()
    {
        if (Instance != null)
        {
            Destroy(gameObject); // уничтожить дубликат
            return;
        }

        Instance = this;
        DontDestroyOnLoad(gameObject);
    }
}
```

```csharp
// В любой сцене — обращение к данным
GameManager.Instance.Score += 100;
```

> ⚠️ Следите за дубликатами: если Bootstrap-сцена загружается повторно, `DontDestroyOnLoad`-объект создастся снова. Всегда проверяйте `Instance != null` в `Awake`.

## Статические поля и классы

Самый простой способ — статический класс-контейнер. Данные живут в памяти до закрытия приложения.

```csharp
public static class GameSession
{
    public static int   Score       { get; set; }
    public static int   Level       { get; set; }
    public static float PlayTime    { get; set; }
    public static string PlayerName { get; set; }
}
```

```csharp
// Сцена 1 — записать
GameSession.Score = 500;
GameSession.Level = 3;
SceneManager.LoadScene("Results");

// Сцена 2 — прочитать
scoreText.text = $"Счёт: {GameSession.Score}";
```

> ⚠️ Статические поля не сериализуются и не сбрасываются при загрузке сцены — сброс нужно делать вручную.

## ScriptableObject как контейнер данных

ScriptableObject-ассет существует как файл в проекте и не привязан ни к одной сцене. Это делает его удобным общим хранилищем данных.

```csharp
// Создание контейнера
[CreateAssetMenu(fileName = "PlayerData", menuName = "Game/PlayerData")]
public class PlayerData : ScriptableObject
{
    public int   Score;
    public int   Lives;
    public float Health;

    public void Reset()
    {
        Score  = 0;
        Lives  = 3;
        Health = 100f;
    }
}
```

```csharp
// В любом MonoBehaviour — ссылка через инспектор
public class ScoreDisplay : MonoBehaviour
{
    [SerializeField] PlayerData _playerData;
    [SerializeField] Text       _scoreText;

    void OnEnable() => _scoreText.text = _playerData.Score.ToString();
}
```

> ⚠️ Изменения в ScriptableObject во время **Play Mode** сохраняются в ассет. Если нужен сброс при старте игры — вызывайте `Reset()` явно в Bootstrap-сцене.

## Сравнение подходов

| Подход | Плюсы | Минусы |
|--------|-------|--------|
| `DontDestroyOnLoad` | Полноценный MonoBehaviour, может иметь компоненты | Нужна осторожность с дубликатами |
| Статический класс | Просто, никакого GameObject | Не виден в инспекторе, сложно тестировать |
| ScriptableObject | Виден в инспекторе, легко переиспользовать | Изменения в Play Mode записываются в ассет |
| `PlayerPrefs` | Сохраняется между сессиями | Только примитивные типы, хранится в реестре/файле |

```csharp
// PlayerPrefs — для простых настроек (громкость, язык и т.п.)
PlayerPrefs.SetInt("HighScore", 1000);
PlayerPrefs.SetFloat("MusicVolume", 0.8f);
PlayerPrefs.SetString("PlayerName", "Alice");
PlayerPrefs.Save(); // явное сохранение на диск

int   highScore = PlayerPrefs.GetInt("HighScore", 0);       // 0 — значение по умолчанию
float volume    = PlayerPrefs.GetFloat("MusicVolume", 1f);
```
