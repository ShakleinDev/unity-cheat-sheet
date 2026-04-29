# Unity Async/Await - Руководство по асинхронному программированию

Async/await — это шаблон программирования, упрощающий асинхронное программирование. Он позволяет писать асинхронный код, который выглядит и ведёт себя как синхронный. Это особенно полезно для операций, которые могут занимать время, например:
- Загрузка ресурсов
- Сетевые запросы
- Операции с файлами
- Загрузка сцен

#### Базовая структура
```csharp
using UnityEngine;
using System.Threading.Tasks;
using System;

public class AsyncAwaitExample : MonoBehaviour
{
    // Базовая структура async/await в Unity
    private async void Start()
    {
        Debug.Log("Начало асинхронной операции");
        await Task.Delay(1000); // Подождать 1 секунду
        Debug.Log("Асинхронная операция завершена");
    }

    // ВАЖНО: методы Update и другие методы MonoBehaviour должны возвращать void
    // Они не могут быть async Task
    private void Update() { }      // Правильно
    // private async Task Update() {} // Неправильно!
}
```

#### Загрузка ресурсов
```csharp
// Асинхронная загрузка ресурсов с отслеживанием прогресса
private async Task<Texture2D> LoadTextureAsync()
{
    var resourcePath = "Textures/MyTexture";
    var request = Resources.LoadAsync<Texture2D>(resourcePath);
    
    while (!request.isDone)
    {
        // Отображать прогресс
        Debug.Log($"Загрузка: {request.progress * 100}%");
        await Task.Yield(); // Дать возможность продолжить другим операциям
    }

    return request.asset as Texture2D;
}
```

#### Веб-запросы
```csharp
// Выполнение веб-запросов с отслеживанием прогресса
private async Task<string> FetchDataAsync(string url)
{
    using var request = UnityWebRequest.Get(url);
    var operation = request.SendWebRequest();

    while (!operation.isDone)
    {
        Debug.Log($"Загрузка: {request.downloadProgress * 100}%");
        await Task.Yield();
    }

    if (request.result != UnityWebRequest.Result.Success)
        throw new Exception($"Ошибка загрузки данных: {request.error}");

    return request.downloadHandler.text;
}
```

#### Загрузка сцен
```csharp
// Асинхронная загрузка сцен с отслеживанием прогресса
private async Task LoadSceneAsync(string sceneName)
{
    var operation = SceneManager.LoadSceneAsync(sceneName);
    operation.allowSceneActivation = false; // Не активировать сразу

    while (operation.progress < 0.9f) // 0.9 — прогресс перед активацией
    {
        Debug.Log($"Загрузка сцены: {operation.progress * 100}%");
        await Task.Yield();
    }

    Debug.Log("Сцена готова к активации");
    operation.allowSceneActivation = true;
}
```

#### Параллельные операции
```csharp
// Запуск нескольких асинхронных операций параллельно
private async Task LoadGameAssetsAsync()
{
    try
    {
        // Запустить несколько операций одновременно
        var textureTask = LoadTextureAsync();
        var dataTask = FetchDataAsync("https://api.example.com/gamedata");
        var sceneTask = LoadSceneAsync("Level1");

        // Дождаться завершения всех задач
        await Task.WhenAll(textureTask, dataTask, sceneTask);
        
        // Все ресурсы загружены
        Debug.Log("Все ресурсы успешно загружены");
    }
    catch (Exception e)
    {
        Debug.LogError($"Ошибка загрузки ресурсов: {e.Message}");
    }
}
```

#### Обработка таймаута
```csharp
// Реализация таймаута для асинхронных операций
private async Task<T> WithTimeout<T>(Task<T> task, TimeSpan timeout)
{
    var timeoutTask = Task.Delay(timeout);
    var completedTask = await Task.WhenAny(task, timeoutTask);
    
    if (completedTask == timeoutTask)
        throw new TimeoutException("Превышено время ожидания операции");
            
    return await task; // Получить результат или передать исключение
}

// Полный пример с обработкой таймаута
private async Task InitializeGameAsync()
{
    try
    {
        // Показать экран загрузки
        ShowLoadingUI();

        // Запустить несколько операций загрузки с таймаутом
        var loadingTask = Task.WhenAll(
            LoadTextureAsync(),
            FetchDataAsync("https://api.example.com/gamedata"),
            LoadSceneAsync("MainLevel")
        );

        // Ожидать завершения всех операций с таймаутом 30 секунд
        await WithTimeout(loadingTask, TimeSpan.FromSeconds(30));

        // Скрыть экран загрузки
        HideLoadingUI();
        Debug.Log("Игра успешно инициализирована");
    }
    catch (TimeoutException)
    {
        Debug.LogError("Превышено время инициализации игры");
        ShowRetryButton();
    }
    catch (Exception e)
    {
        Debug.LogError($"Ошибка инициализации игры: {e.Message}");
        ShowErrorUI();
    }
}
```

#### Лучшие практики
При использовании async/await в Unity придерживайтесь следующих рекомендаций:

1. Всегда обрабатывайте исключения в асинхронных методах
2. Используйте `Task.Yield()` вместо `Task.Delay()` для покадровых операций
3. Помните, что `async void` следует использовать только для обработчиков событий и методов MonoBehaviour
4. По возможности используйте `CancellationToken` для поддержки отмены операций
5. Реализуйте обработку таймаута для сетевых операций
6. Не блокируйте главный поток с помощью `.Result` или `.Wait()`
7. Используйте `Task.WhenAll` для параллельных операций

