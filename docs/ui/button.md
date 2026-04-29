# Unity UI Button — События OnClick

```csharp
// Button используется для обработки кликов и взаимодействий пользователя.
// Прикрепите этот скрипт к компоненту Button для реагирования на нажатия кнопки.

using UnityEngine.UI;

Button myButton = GetComponent<Button>();
myButton.onClick.AddListener(MyButtonClickHandler);

void MyButtonClickHandler() {
    Debug.Log("Кнопка нажата!");
}
```

