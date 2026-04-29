# Unity Coroutines - Операции с задержкой и управление временем

Корутины в Unity — это мощный инструмент, позволяющий приостанавливать выполнение функции и возобновлять его позже. Это особенно полезно для задач, которые нужно растянуть на несколько кадров: анимации, ожидание выполнения условия или обработка асинхронных операций.

#### Базовый пример корутины
```csharp
using UnityEngine;
using System.Collections;

public class CoroutineExample : MonoBehaviour {
    void Start() {
        // Запустить корутину
        StartCoroutine(ExampleCoroutine());
    }

    IEnumerator ExampleCoroutine() {
        Debug.Log("Корутина запущена");

        // Подождать 2 секунды
        yield return new WaitForSeconds(2);

        Debug.Log("Корутина возобновлена после 2 секунд");
    }
}
```

#### Использование корутин для повторяющихся действий
Корутины можно использовать для выполнения повторяющихся действий с задержкой между итерациями.

```csharp
IEnumerator RepeatActionCoroutine() {
    while (true) {
        Debug.Log("Действие выполнено");
        
        // Подождать 1 секунду перед повтором
        yield return new WaitForSeconds(1);
    }
}

// Запустить корутину
StartCoroutine(RepeatActionCoroutine());
```

#### Ожидание условия
Корутины также могут ждать выполнения условия перед продолжением.

```csharp
IEnumerator WaitForConditionCoroutine() {
    Debug.Log("Ожидание условия...");

    // Ждать, пока условие не выполнится
    yield return new WaitUntil(() => SomeConditionIsTrue());

    Debug.Log("Условие выполнено, корутина возобновлена");
}

bool SomeConditionIsTrue() {
    // Замените на своё реальное условие
    return Time.time > 5;
}

// Запустить корутину
StartCoroutine(WaitForConditionCoroutine());
```

#### Использование корутин с событиями Unity
Корутины можно использовать для обработки событий во времени, например для плавного скрытия UI-элемента.

```csharp
IEnumerator FadeOutCoroutine(CanvasGroup canvasGroup, float duration) {
    float startAlpha = canvasGroup.alpha;
    float rate = 1.0f / duration;
    float progress = 0.0f;

    while (progress < 1.0f) {
        canvasGroup.alpha = Mathf.Lerp(startAlpha, 0, progress);
        progress += rate * Time.deltaTime;

        yield return null; // Подождать следующий кадр
    }

    canvasGroup.alpha = 0;
}

// Использование
CanvasGroup myCanvasGroup = GetComponent<CanvasGroup>();
StartCoroutine(FadeOutCoroutine(myCanvasGroup, 2.0f));
```

#### Остановка корутин
Вы можете остановить корутину с помощью `StopCoroutine()` или `StopAllCoroutines()`.

```csharp
Coroutine myCoroutine;

void Start() {
    myCoroutine = StartCoroutine(ExampleCoroutine());
}

void StopMyCoroutine() {
    if (myCoroutine != null) {
        StopCoroutine(myCoroutine);
    }
}

void StopAllMyCoroutines() {
    StopAllCoroutines();
}
```

#### Важные замечания
- Корутины не являются потоками. Они выполняются в главном потоке и подчиняются тем же ограничениям производительности.
- Используйте `yield return null;` для ожидания следующего кадра.
- Используйте `yield return new WaitForSeconds(seconds);` для ожидания определённого времени.
- Используйте `yield return new WaitUntil(() => condition);` для ожидания выполнения условия.
- Корутины можно вкладывать друг в друга — можно делать `yield return` на другую корутину.

