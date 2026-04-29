# Unity Ввод мыши — Устаревший менеджер ввода

> **Примечание:** Эта страница описывает **Устаревший менеджер ввода (Legacy Input Manager)**. Для проектов на Unity 6+ рекомендуется использовать [Новую систему ввода](new-input-system.md), которая теперь является стандартной.

```csharp
if (Input.GetAxis("Mouse X") < 0) {
    Debug.Log("Мышь движется влево");
}

if (Input.GetAxis("Mouse Y") > 0) {
    Debug.Log("Мышь движется вверх");
}

if (Input.GetMouseButtonDown(0)) {
    Debug.Log("Нажата основная кнопка.");
}

if (Input.GetMouseButtonDown(1)) {
    Debug.Log("Нажата дополнительная кнопка.");
}

if (Input.GetMouseButtonDown(2)) {
    Debug.Log("Нажата средняя кнопка мыши.");
}
```

