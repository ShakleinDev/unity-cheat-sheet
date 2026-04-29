# Unity Load Scene - Руководство по управлению сценами

Переход к следующей сцене по индексу сборки после завершения уровня или катсцены. Защитите вызов проверкой, чтобы загрузка происходила только при наличии следующей сцены и не вызывала ошибок индекса.

## Поведение

1. Получить индекс сборки активной сцены.
2. Прибавить единицу для получения следующего индекса.
3. Сравнить с `SceneManager.sceneCountInBuildSettings`, чтобы убедиться в существовании сцены.
4. Загрузить следующую сцену синхронно (`LoadScene`) или асинхронно (`LoadSceneAsync`).

## Пример

```csharp
using UnityEngine;
using UnityEngine.SceneManagement;

public class LoadNextSceneOnTrigger : MonoBehaviour
{
    [SerializeField] private string loadingScreenScene; // опциональная аддитивная сцена для переходов

    public void LoadNext()
    {
        int currentIndex = SceneManager.GetActiveScene().buildIndex;
        int nextIndex = currentIndex + 1;
        int sceneCount = SceneManager.sceneCountInBuildSettings;

        if (nextIndex >= sceneCount)
        {
            Debug.Log("Больше нет сцен для загрузки.");
            return;
        }

        if (!string.IsNullOrEmpty(loadingScreenScene))
        {
            SceneManager.LoadScene(loadingScreenScene, LoadSceneMode.Additive);
        }

        SceneManager.LoadScene(nextIndex);
    }
}
```

### Примечания

- Используйте `LoadSceneAsync`, если хотите отобразить UI загрузки или стримить большие уровни.
- При изменении порядка сцен не забывайте обновлять индексы сборки через **File → Build Settings**.
- Для зацикленного геймплея (например, бесконечный раннер) управляйте индексом вручную или возвращайтесь в сцену меню вместо логирования.
- В аддитивных сценариях выгружайте сцену загрузки или предыдущие сцены после готовности новой, чтобы избежать дублирования.
