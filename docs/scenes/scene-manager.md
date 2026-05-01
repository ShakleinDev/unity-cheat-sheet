# SceneManager: основы

`SceneManager` — статический класс из пространства имён `UnityEngine.SceneManagement`, отвечающий за загрузку, выгрузку и получение информации о сценах.

```csharp
using UnityEngine.SceneManagement;
```

## Загрузка и выгрузка сцен

```csharp
// Загрузка по имени (имя файла сцены без расширения)
SceneManager.LoadScene("MainMenu");

// Загрузка по индексу из Build Settings
SceneManager.LoadScene(0);

// Асинхронная загрузка (рекомендуется — см. async-loading.md)
SceneManager.LoadSceneAsync("GameLevel");

// Выгрузка сцены (только для аддитивно загруженных сцен)
SceneManager.UnloadSceneAsync("HUD");
SceneManager.UnloadSceneAsync(2);
```

## Перезагрузка текущей сцены

```csharp
// Получить индекс текущей сцены и загрузить её заново
SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex);

// Или по имени
SceneManager.LoadScene(SceneManager.GetActiveScene().name);
```

## Получение информации о сцене

```csharp
// Активная сцена
Scene active = SceneManager.GetActiveScene();

Debug.Log(active.name);         // имя сцены
Debug.Log(active.buildIndex);   // индекс в Build Settings (-1, если не добавлена)
Debug.Log(active.isLoaded);     // загружена ли сцена
Debug.Log(active.path);         // путь к файлу сцены в проекте

// Количество загруженных сцен (актуально при аддитивной загрузке)
int count = SceneManager.sceneCount;

// Получить сцену по индексу среди загруженных
Scene scene = SceneManager.GetSceneAt(0);

// Получить сцену по имени
Scene byName = SceneManager.GetSceneByName("HUD");

// Проверить, загружена ли сцена
bool isLoaded = SceneManager.GetSceneByName("HUD").isLoaded;
```

## Настройка Build Settings

Только сцены, добавленные в **Build Settings**, можно загружать по индексу или имени в сборке.

1. Открыть *File → Build Settings*
2. Нажать **Add Open Scenes** или перетащить сцены вручную
3. Порядок сцен в списке соответствует их `buildIndex`

```csharp
// Типичное соглашение об индексах
// 0 — Bootstrap / Preload (инициализация менеджеров)
// 1 — MainMenu
// 2+ — игровые уровни

// Загрузка следующего уровня
int next = SceneManager.GetActiveScene().buildIndex + 1;
if (next < SceneManager.sceneCountInBuildSettings)
    SceneManager.LoadScene(next);
else
    SceneManager.LoadScene(0); // вернуться в главное меню
```
