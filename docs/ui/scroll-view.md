# ScrollView (ScrollRect)

`ScrollRect` — компонент для создания прокручиваемых списков, чатов, инвентарей и любых контейнеров с большим содержимым.

## Структура ScrollView

```
ScrollView (ScrollRect + Mask + Image)
└── Viewport (Mask + Image)
    └── Content (VerticalLayoutGroup + ContentSizeFitter)
        ├── Item 1
        ├── Item 2
        └── Item 3
```

## Базовое управление

```csharp
using UnityEngine.UI;

ScrollRect scrollRect = GetComponent<ScrollRect>();

// Прокрутить в начало (вверх)
scrollRect.verticalNormalizedPosition = 1f;

// Прокрутить в конец (вниз)
scrollRect.verticalNormalizedPosition = 0f;

// Прокрутить в конкретную позицию (0..1)
scrollRect.verticalNormalizedPosition = 0.5f; // середина

// Горизонтальная прокрутка
scrollRect.horizontalNormalizedPosition = 0f; // лево
scrollRect.horizontalNormalizedPosition = 1f; // право

// Отключить прокрутку по оси
scrollRect.vertical   = false;
scrollRect.horizontal = true;
```

## Инерция и эластичность

```csharp
// Инерция — продолжать движение после отпускания
scrollRect.inertia = true;
scrollRect.decelerationRate = 0.135f; // 0 = мгновенная остановка, 1 = не останавливается

// Ограничение выхода за границы
scrollRect.movementType = ScrollRect.MovementType.Clamped;    // жёсткое ограничение
scrollRect.movementType = ScrollRect.MovementType.Elastic;    // пружинный отскок
scrollRect.movementType = ScrollRect.MovementType.Unrestricted; // без ограничений
scrollRect.elasticity = 0.1f; // жёсткость пружины
```

## Событие прокрутки

```csharp
scrollRect.onValueChanged.AddListener(OnScroll);

void OnScroll(Vector2 normalizedPos)
{
    Debug.Log($"Прокрутка: {normalizedPos}");

    // Подгрузка данных при приближении к концу
    if (normalizedPos.y < 0.1f)
        LoadMoreItems();
}
```

## Прокрутка к конкретному элементу

```csharp
[SerializeField] ScrollRect scrollRect;
[SerializeField] RectTransform content;

/// <summary>Прокрутить так, чтобы targetItem был виден</summary>
void ScrollToItem(RectTransform targetItem)
{
    Canvas.ForceUpdateCanvases();

    Vector2 contentPos = (Vector2)scrollRect.transform.InverseTransformPoint(content.position)
                       - (Vector2)scrollRect.transform.InverseTransformPoint(targetItem.position);

    contentPos.x = content.anchoredPosition.x;
    content.anchoredPosition = contentPos;
}
```

## Плавная прокрутка к элементу

```csharp
IEnumerator SmoothScrollTo(RectTransform target, float duration = 0.3f)
{
    Canvas.ForceUpdateCanvases();

    float startPos = scrollRect.verticalNormalizedPosition;

    // Вычислить нужную нормализованную позицию
    float contentHeight = content.rect.height - scrollRect.viewport.rect.height;
    float targetY = content.rect.height - (-target.anchoredPosition.y + target.rect.height);
    float endPos = Mathf.Clamp01(targetY / contentHeight);

    float elapsed = 0f;
    while (elapsed < duration)
    {
        elapsed += Time.deltaTime;
        scrollRect.verticalNormalizedPosition = Mathf.Lerp(startPos, endPos, elapsed / duration);
        yield return null;
    }
    scrollRect.verticalNormalizedPosition = endPos;
}
```

## Динамическое добавление элементов с авто-прокруткой вниз

```csharp
[SerializeField] ScrollRect chatScroll;
[SerializeField] RectTransform chatContent;
[SerializeField] GameObject messagePrefab;

void AddMessage(string text)
{
    GameObject msg = Instantiate(messagePrefab, chatContent);
    msg.GetComponentInChildren<TextMeshProUGUI>().text = text;

    // Обновить layout и прокрутить вниз в следующем кадре
    StartCoroutine(ScrollToBottomNextFrame());
}

IEnumerator ScrollToBottomNextFrame()
{
    yield return new WaitForEndOfFrame();
    chatScroll.verticalNormalizedPosition = 0f;
}
```

## Скроллбар — управление из кода

```csharp
Scrollbar scrollbar = GetComponentInChildren<Scrollbar>();

// Текущее значение (0..1)
float pos = scrollbar.value;

// Установить позицию
scrollbar.value = 0f;

// Показать / скрыть автоматически
scrollbar.gameObject.SetActive(needsScrollbar);
```

## Рекомендации

- Для длинных списков (100+ элементов) используйте **виртуализацию** — показывайте только видимые элементы, переиспользуя пул объектов. Стандартный ScrollRect не виртуализирует.
- Всегда ставьте `Content Size Fitter` (`verticalFit = PreferredSize`) на Content, чтобы он автоматически растягивался.
- Прокрутку к нижнему элементу делайте через `WaitForEndOfFrame` — к этому моменту Layout уже пересчитан.
- Отключайте ось прокрутки, которая не нужна (`horizontal = false`), чтобы избежать случайного смещения.
