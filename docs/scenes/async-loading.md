# Асинхронная загрузка

`LoadSceneAsync` загружает сцену в фоне, не блокируя главный поток. Это позволяет показывать экран загрузки и анимации во время подготовки сцены.

## LoadSceneAsync и прогресс загрузки

```csharp
using System.Collections;
using UnityEngine.SceneManagement;

IEnumerator LoadSceneWithProgress(string sceneName)
{
    AsyncOperation operation = SceneManager.LoadSceneAsync(sceneName);

    while (!operation.isDone)
    {
        // progress идёт от 0.0 до 0.9, затем прыгает до 1.0 при активации
        float progress = Mathf.Clamp01(operation.progress / 0.9f);

        Debug.Log($"Загрузка: {progress * 100:0}%");

        yield return null;
    }
}
```

## allowSceneActivation

По умолчанию сцена активируется сразу после загрузки. Установив `allowSceneActivation = false`, можно удержать переход — например, дождаться окончания анимации или набрать минимальное время показа экрана загрузки.

```csharp
IEnumerator LoadAndHold(string sceneName)
{
    AsyncOperation operation = SceneManager.LoadSceneAsync(sceneName);

    // Запрещаем автоматическую активацию
    operation.allowSceneActivation = false;

    // Ждём полной загрузки (progress достигает 0.9)
    while (operation.progress < 0.9f)
    {
        float progress = operation.progress / 0.9f;
        UpdateProgressBar(progress);
        yield return null;
    }

    // Сцена готова, ждём нажатия кнопки
    UpdateProgressBar(1f);
    ShowPressAnyKeyPrompt();

    yield return new WaitUntil(() => Input.anyKeyDown);

    // Активируем сцену
    operation.allowSceneActivation = true;
}
```

## Экран загрузки с прогресс-баром

```csharp
using System.Collections;
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.SceneManagement;

public class SceneLoader : MonoBehaviour
{
    [SerializeField] GameObject _loadingScreen;
    [SerializeField] Slider     _progressBar;
    [SerializeField] float      _minLoadTime = 1.5f; // минимальное время показа

    public void LoadScene(string sceneName)
    {
        StartCoroutine(LoadRoutine(sceneName));
    }

    IEnumerator LoadRoutine(string sceneName)
    {
        _loadingScreen.SetActive(true);
        _progressBar.value = 0f;

        AsyncOperation operation = SceneManager.LoadSceneAsync(sceneName);
        operation.allowSceneActivation = false;

        float elapsed = 0f;

        while (operation.progress < 0.9f || elapsed < _minLoadTime)
        {
            elapsed += Time.deltaTime;

            // Учитываем и прогресс загрузки, и минимальное время
            float loadProgress = operation.progress / 0.9f;
            float timeProgress = elapsed / _minLoadTime;
            _progressBar.value = Mathf.Min(loadProgress, timeProgress);

            yield return null;
        }

        _progressBar.value = 1f;

        // Небольшая пауза перед переходом
        yield return new WaitForSeconds(0.2f);

        operation.allowSceneActivation = true;
    }
}
```
