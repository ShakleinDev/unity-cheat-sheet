# События SceneManager

SceneManager предоставляет три статических события для реакции на изменения состояния сцен.

## sceneLoaded

Вызывается после полной загрузки и активации сцены.

```csharp
using UnityEngine.SceneManagement;

public class SceneEventListener : MonoBehaviour
{
    void OnEnable()
    {
        SceneManager.sceneLoaded += OnSceneLoaded;
    }

    void OnDisable()
    {
        SceneManager.sceneLoaded -= OnSceneLoaded;
    }

    void OnSceneLoaded(Scene scene, LoadSceneMode mode)
    {
        Debug.Log($"Загружена сцена: {scene.name}, режим: {mode}");

        // Инициализация в зависимости от сцены
        if (scene.name == "GameLevel")
            InitializeLevel();
    }

    void InitializeLevel()
    {
        // Найти стартовую точку, разместить игрока и т.п.
        Transform spawnPoint = GameObject.FindWithTag("SpawnPoint")?.transform;
        if (spawnPoint != null)
            FindObjectOfType<PlayerController>().transform.position = spawnPoint.position;
    }
}
```

## sceneUnloaded и activeSceneChanged

```csharp
void OnEnable()
{
    SceneManager.sceneUnloaded      += OnSceneUnloaded;
    SceneManager.activeSceneChanged += OnActiveSceneChanged;
}

void OnDisable()
{
    SceneManager.sceneUnloaded      -= OnSceneUnloaded;
    SceneManager.activeSceneChanged -= OnActiveSceneChanged;
}

// Вызывается после выгрузки сцены
void OnSceneUnloaded(Scene scene)
{
    Debug.Log($"Выгружена сцена: {scene.name}");

    // Очистить кэши, пулы объектов, временные данные
    ObjectPool.Instance?.Clear();
}

// Вызывается при смене активной сцены
// previousScene может быть невалидной при первой загрузке
void OnActiveSceneChanged(Scene previousScene, Scene newScene)
{
    Debug.Log($"Активная сцена изменена: {previousScene.name} → {newScene.name}");
}
```

## Отписка от событий

Всегда отписывайтесь от событий SceneManager, чтобы избежать утечек и вызовов на уже уничтоженных объектах.

```csharp
// Правильный паттерн: подписка в OnEnable, отписка в OnDisable
public class SceneListener : MonoBehaviour
{
    void OnEnable()  => SceneManager.sceneLoaded += OnSceneLoaded;
    void OnDisable() => SceneManager.sceneLoaded -= OnSceneLoaded;

    void OnSceneLoaded(Scene scene, LoadSceneMode mode) { /* ... */ }
}
```

```csharp
// Для DontDestroyOnLoad-объектов — отписка в OnDestroy
public class PersistentManager : MonoBehaviour
{
    void Awake()
    {
        DontDestroyOnLoad(gameObject);
        SceneManager.sceneLoaded += OnSceneLoaded;
    }

    void OnDestroy()
    {
        SceneManager.sceneLoaded -= OnSceneLoaded;
    }

    void OnSceneLoaded(Scene scene, LoadSceneMode mode)
    {
        // Реинициализация систем после загрузки каждой сцены
        AudioManager.Instance?.UpdateMixerForScene(scene.name);
        UIManager.Instance?.SetupHUD(scene.name);
    }
}
```

```csharp
// Одноразовая подписка — лямбда через локальную переменную
void LoadAndThenDo(string sceneName, System.Action onLoaded)
{
    System.Action<Scene, LoadSceneMode> handler = null;

    handler = (scene, mode) =>
    {
        if (scene.name == sceneName)
        {
            onLoaded?.Invoke();
            SceneManager.sceneLoaded -= handler; // отписаться после первого срабатывания
        }
    };

    SceneManager.sceneLoaded += handler;
    SceneManager.LoadSceneAsync(sceneName, LoadSceneMode.Additive);
}

// Использование
LoadAndThenDo("HUD", () => Debug.Log("HUD готов"));
```
