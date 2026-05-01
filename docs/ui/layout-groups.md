# Layout Groups — автоматическое расположение элементов

Layout Groups автоматически выравнивают и распределяют дочерние элементы внутри контейнера. Избавляют от ручной расстановки позиций.

## Horizontal Layout Group

Располагает дочерние элементы в ряд по горизонтали.

```csharp
using UnityEngine.UI;

HorizontalLayoutGroup hlg = GetComponent<HorizontalLayoutGroup>();

// Отступы контейнера
hlg.padding = new RectOffset(left: 10, right: 10, top: 5, bottom: 5);

// Расстояние между элементами
hlg.spacing = 8f;

// Выравнивание дочерних элементов
hlg.childAlignment = TextAnchor.MiddleCenter;

// Управление размером дочерних элементов
hlg.childControlWidth  = true;  // группа управляет шириной
hlg.childControlHeight = true;  // группа управляет высотой
hlg.childForceExpandWidth  = true;  // растягивать по ширине
hlg.childForceExpandHeight = false; // не растягивать по высоте
```

## Vertical Layout Group

Аналогичен Horizontal, но элементы располагаются сверху вниз.

```csharp
VerticalLayoutGroup vlg = GetComponent<VerticalLayoutGroup>();

vlg.padding    = new RectOffset(10, 10, 10, 10);
vlg.spacing    = 4f;
vlg.childAlignment        = TextAnchor.UpperCenter;
vlg.childControlHeight    = true;
vlg.childForceExpandWidth = true;
```

## Grid Layout Group

Располагает элементы в сетку с фиксированным размером ячеек.

```csharp
GridLayoutGroup grid = GetComponent<GridLayoutGroup>();

// Размер каждой ячейки
grid.cellSize = new Vector2(100f, 100f);

// Отступы и расстояния
grid.padding = new RectOffset(10, 10, 10, 10);
grid.spacing = new Vector2(8f, 8f);

// Направление заполнения
grid.startCorner = GridLayoutGroup.Corner.UpperLeft;
grid.startAxis   = GridLayoutGroup.Axis.Horizontal; // заполнять по строкам

// Ограничение числа колонок или строк
grid.constraint      = GridLayoutGroup.Constraint.FixedColumnCount;
grid.constraintCount = 4; // 4 колонки

// Или фиксированное число строк
grid.constraint      = GridLayoutGroup.Constraint.FixedRowCount;
grid.constraintCount = 3;
```

## Layout Element — переопределение размера отдельного элемента

`LayoutElement` позволяет задать минимальный, предпочтительный или гибкий размер для конкретного дочернего объекта.

```csharp
LayoutElement le = GetComponent<LayoutElement>();

le.minWidth      = 50f;
le.preferredWidth  = 150f;
le.flexibleWidth   = 1f;  // занять оставшееся место (1 = приоритет)

le.minHeight     = 30f;
le.preferredHeight = 60f;
le.flexibleHeight  = 0f;

// Полностью отключить Layout для этого элемента
le.ignoreLayout = true;
```

## Content Size Fitter — автоподгон контейнера под содержимое

```csharp
ContentSizeFitter fitter = GetComponent<ContentSizeFitter>();

// Подгонять ширину под содержимое
fitter.horizontalFit = ContentSizeFitter.FitMode.PreferredSize;

// Подгонять высоту под содержимое (для чата, описаний)
fitter.verticalFit = ContentSizeFitter.FitMode.PreferredSize;

// Не подгонять
fitter.horizontalFit = ContentSizeFitter.FitMode.Unconstrained;
```

## Пример: динамический список элементов

```csharp
[SerializeField] VerticalLayoutGroup container;
[SerializeField] GameObject itemPrefab;

void PopulateList(string[] items)
{
    // Очистить старые элементы
    foreach (Transform child in container.transform)
        Destroy(child.gameObject);

    // Создать новые
    foreach (string text in items)
    {
        GameObject obj = Instantiate(itemPrefab, container.transform);
        obj.GetComponentInChildren<TextMeshProUGUI>().text = text;
    }
}
```

## Принудительное обновление разметки

```csharp
// Если после изменения данных Layout не пересчитался
LayoutRebuilder.ForceRebuildLayoutImmediate(container.GetComponent<RectTransform>());

// Или в следующем кадре (мягче, без лагов)
LayoutRebuilder.MarkLayoutForRebuild(container.GetComponent<RectTransform>());
```

## Рекомендации

- **Не вкладывайте** Layout Groups глубоко — каждый уровень вложенности умножает стоимость пересчёта.
- Для статичного UI **отключайте** Layout Group после построения (`enabled = false`) — нет смысла пересчитывать каждый кадр.
- `ContentSizeFitter` + `VerticalLayoutGroup` — стандартная связка для чатов и списков с переменной высотой строк.
- `LayoutElement.ignoreLayout = true` позволяет "выбить" один элемент из автоматической разметки, не ломая остальные.
