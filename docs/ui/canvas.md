# Canvas, CanvasScaler и GraphicRaycaster

## Canvas — режимы рендеринга

```csharp
// Три режима рендеринга Canvas:
// Screen Space - Overlay  — поверх всего, не зависит от камеры (по умолчанию)
// Screen Space - Camera   — привязан к камере, учитывает расстояние
// World Space             — 3D-объект в мировом пространстве (для диегетического UI)

Canvas canvas = GetComponent<Canvas>();

// Установить режим из кода
canvas.renderMode = RenderMode.ScreenSpaceOverlay;
canvas.renderMode = RenderMode.ScreenSpaceCamera;
canvas.renderMode = RenderMode.WorldSpace;

// Назначить камеру для режима ScreenSpaceCamera
canvas.worldCamera = Camera.main;
canvas.planeDistance = 1f; // дистанция до камеры
```

## CanvasScaler — масштабирование UI

```csharp
using UnityEngine.UI;

CanvasScaler scaler = GetComponent<CanvasScaler>();

// Constant Pixel Size — размер в пикселях (не масштабируется)
scaler.uiScaleMode = CanvasScaler.ScaleMode.ConstantPixelSize;
scaler.scaleFactor = 1f;

// Scale With Screen Size — масштабируется под разрешение (рекомендуется)
scaler.uiScaleMode = CanvasScaler.ScaleMode.ScaleWithScreenSize;
scaler.referenceResolution = new Vector2(1920, 1080);
scaler.screenMatchMode = CanvasScaler.ScreenMatchMode.MatchWidthOrHeight;
scaler.matchWidthOrHeight = 0.5f; // 0 = по ширине, 1 = по высоте, 0.5 = среднее

// Constant Physical Size — масштабируется под физический размер экрана
scaler.uiScaleMode = CanvasScaler.ScaleMode.ConstantPhysicalSize;
```

## GraphicRaycaster — обработка кликов

```csharp
using UnityEngine.UI;

GraphicRaycaster raycaster = GetComponent<GraphicRaycaster>();

// Отключить все клики на Canvas (например, во время загрузки)
raycaster.enabled = false;

// Включить обратно
raycaster.enabled = true;
```

## Управление порядком отрисовки (Sorting)

```csharp
Canvas canvas = GetComponent<Canvas>();

// Переопределить Sorting Order для этого Canvas
canvas.overrideSorting = true;
canvas.sortingOrder = 10; // выше = поверх других

// Назначить Sorting Layer
canvas.sortingLayerName = "UI";
canvas.sortingLayerID = SortingLayer.NameToID("UI");
```

## CanvasGroup — групповое управление прозрачностью и вводом

```csharp
using UnityEngine.UI;

CanvasGroup group = GetComponent<CanvasGroup>();

// Прозрачность всей группы
group.alpha = 0.5f;

// Запретить взаимодействие (клики не проходят)
group.interactable = false;

// Отключить raycast (элементы не перехватывают события мыши)
group.blocksRaycasts = false;

// Игнорировать родительский CanvasGroup
group.ignoreParentGroups = true;
```

## Рекомендации

- Используйте **Scale With Screen Size** и `referenceResolution = 1920×1080` — это покрывает большинство платформ.
- Для мобильных устройств ставьте `matchWidthOrHeight = 0` (по ширине) для landscape, `1` (по высоте) для portrait.
- Не создавайте лишние Canvas — каждый Canvas — отдельный draw call батч. Группируйте элементы.
- Используйте `CanvasGroup` для fade-in/out целых панелей вместо изменения alpha каждого элемента.
- Для 3D-мира (healthbar над головой) используйте **World Space Canvas** с `Billboard`-скриптом.
