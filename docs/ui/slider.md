# Unity UI Slider — События изменения значения

```csharp
// Slider используется для выбора значения в заданном диапазоне.
// Прикрепите этот скрипт к компоненту Slider для реагирования на изменения значения.

using UnityEngine.UI;

Slider mySlider = GetComponent<Slider>();
mySlider.onValueChanged.AddListener(MySliderValueChangedHandler);

void MySliderValueChangedHandler(float value) {
    Debug.Log("Значение слайдера: " + value);
}
```

