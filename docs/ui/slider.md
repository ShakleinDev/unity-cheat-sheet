# UI Slider

## Базовое использование

```csharp
using UnityEngine.UI;

Slider slider = GetComponent<Slider>();

// Текущее значение
float value = slider.value;

// Установить значение
slider.value = 0.75f;

// Установить без вызова события
slider.SetValueWithoutNotify(0.75f);

// Диапазон значений
slider.minValue = 0f;
slider.maxValue = 100f;

// Только целые числа
slider.wholeNumbers = true;
```

## Событие изменения значения

```csharp
slider.onValueChanged.AddListener(OnVolumeChanged);

void OnVolumeChanged(float value)
{
    Debug.Log($"Громкость: {value}");
    AudioListener.volume = value;
}

void OnDestroy()
{
    slider.onValueChanged.RemoveListener(OnVolumeChanged);
}
```

## Слайдер громкости

```csharp
[SerializeField] Slider volumeSlider;
[SerializeField] TextMeshProUGUI volumeLabel;

void Start()
{
    volumeSlider.minValue = 0f;
    volumeSlider.maxValue = 1f;
    volumeSlider.value = PlayerPrefs.GetFloat("Volume", 1f);

    volumeSlider.onValueChanged.AddListener(v =>
    {
        AudioListener.volume = v;
        volumeLabel.text = $"{Mathf.RoundToInt(v * 100)}%";
        PlayerPrefs.SetFloat("Volume", v);
    });
}
```

## HP-бар (только визуальный, без взаимодействия)

```csharp
[SerializeField] Slider hpSlider;

void Start()
{
    hpSlider.interactable = false; // только отображение
    hpSlider.minValue = 0f;
    hpSlider.maxValue = 100f;
}

void UpdateHp(float current, float max)
{
    hpSlider.value = current / max * hpSlider.maxValue;
}
```

## Управление цветом заполнения

```csharp
[SerializeField] Slider hpSlider;
[SerializeField] Image fillImage;

void UpdateColor(float normalized)
{
    // Зелёный → Жёлтый → Красный
    fillImage.color = Color.Lerp(Color.red, Color.green, normalized);
}
```

## Ориентация

```csharp
// Горизонтальный (по умолчанию)
slider.direction = Slider.Direction.LeftToRight;
slider.direction = Slider.Direction.RightToLeft;

// Вертикальный
slider.direction = Slider.Direction.BottomToTop;
slider.direction = Slider.Direction.TopToBottom;
```

## Рекомендации

- Для HP-баров и полос загрузки устанавливайте `interactable = false` — пользователь не сможет перетащить ползунок.
- Используйте `SetValueWithoutNotify` при инициализации слайдера из сохранения, чтобы не вызывать события.
- Для целочисленных параметров (например, уровень качества 0–3) включайте `wholeNumbers = true`.
