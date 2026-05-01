# Аддитивная загрузка

## Single vs Additive

| Режим | Поведение |
|-------|-----------|
| `LoadSceneMode.Single` | Выгружает все текущие сцены, загружает новую. Поведение по умолчанию. |
| `LoadSceneMode.Additive` | Добавляет сцену поверх уже загруженных, не выгружая их. |

```csharp
using UnityEngine.SceneManagement;

// Single — стандартная смена сцены
SceneManager.LoadScene("Level_01");

// Additive — добавить сцену к текущей
SceneManager.LoadScene("HUD", LoadSceneMode.Additive);
SceneManager.LoadSceneAsync("HUD", LoadSceneMode.Additive);
```

## Установка активной сцены

При аддитивной загрузке «активной» остаётся та сцена, в которую будут создаваться новые объекты через `Instantiate`. Активную сцену можно сменить явно.

```csharp
// Установить активную сцену после её загрузки
IEnumerator LoadAndActivate(string sceneName)
{
    AsyncOperation op = SceneManager.LoadSceneAsync(sceneName, LoadSceneMode.Additive);
    yield return op;

    Scene loaded = SceneManager.GetSceneByName(sceneName);
    SceneManager.SetActiveScene(loaded);
}
```

## Перемещение объектов между сценами

```csharp
// Переместить GameObject в конкретную сцену
Scene targetScene = SceneManager.GetSceneByName("Level_01");
SceneManager.MoveGameObjectToScene(gameObject, targetScene);

// Полезно для объектов с DontDestroyOnLoad —
// вернуть их в обычную сцену после загрузки уровня
void MoveManagerToScene(GameObject manager, string sceneName)
{
    Scene scene = SceneManager.GetSceneByName(sceneName);
    if (scene.isLoaded)
        SceneManager.MoveGameObjectToScene(manager, scene);
}
```

## Практические примеры

### Постоянная UI-сцена + игровые уровни

```csharp
// Bootstrap.cs — загружается первой (buildIndex = 0)
public class Bootstrap : MonoBehaviour
{
    IEnumerator Start()
    {
        // Загружаем постоянный HUD аддитивно
        yield return SceneManager.LoadSceneAsync("HUD", LoadSceneMode.Additive);

        // Загружаем главное меню
        yield return SceneManager.LoadSceneAsync("MainMenu", LoadSceneMode.Additive);

        Scene mainMenu = SceneManager.GetSceneByName("MainMenu");
        SceneManager.SetActiveScene(mainMenu);
    }
}
```

```csharp
// LevelManager.cs — смена игровых уровней без выгрузки HUD
public class LevelManager : MonoBehaviour
{
    string _currentLevel;

    public IEnumerator GoToLevel(string levelName)
    {
        // Выгружаем старый уровень
        if (!string.IsNullOrEmpty(_currentLevel))
            yield return SceneManager.UnloadSceneAsync(_currentLevel);

        // Загружаем новый уровень аддитивно
        yield return SceneManager.LoadSceneAsync(levelName, LoadSceneMode.Additive);

        Scene level = SceneManager.GetSceneByName(levelName);
        SceneManager.SetActiveScene(level);

        _currentLevel = levelName;
    }
}
```

### Стриминг мира по чанкам

```csharp
// Загружаем соседние чанки по мере движения игрока
public class WorldStreamer : MonoBehaviour
{
    [SerializeField] Transform _player;
    [SerializeField] float     _loadRadius   = 100f;
    [SerializeField] float     _unloadRadius = 150f;

    HashSet<string> _loadedChunks = new();

    void Update()
    {
        Vector2Int chunk = WorldToChunk(_player.position);

        for (int x = -1; x <= 1; x++)
        for (int z = -1; z <= 1; z++)
        {
            string name = ChunkName(chunk.x + x, chunk.y + z);
            if (!_loadedChunks.Contains(name))
                StartCoroutine(LoadChunk(name));
        }
    }

    IEnumerator LoadChunk(string name)
    {
        _loadedChunks.Add(name);
        yield return SceneManager.LoadSceneAsync(name, LoadSceneMode.Additive);
    }

    Vector2Int WorldToChunk(Vector3 pos) =>
        new Vector2Int(Mathf.FloorToInt(pos.x / 100f), Mathf.FloorToInt(pos.z / 100f));

    string ChunkName(int x, int z) => $"Chunk_{x}_{z}";
}
```
