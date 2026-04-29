# Unity Сенсорный Ввод - Устаревший Менеджер Ввода

> **Примечание:** Эта страница описывает **Устаревший Менеджер Ввода**. Для проектов на Unity 6+ используйте [Новую Систему Ввода](new-input-system.md), которая теперь является стандартом.

```csharp
if (Input.touchCount > 0) {
    touch = Input.GetTouch(0);

    if (touch.phase == TouchPhase.Began) {
        Debug.Log("Касание началось");
    }

    if (touch.phase == TouchPhase.Moved) {
        Debug.Log("Касание перемещается");
    }

    if (touch.phase == TouchPhase.Ended) {
        Debug.Log("Касание завершено");
    }
}
```

