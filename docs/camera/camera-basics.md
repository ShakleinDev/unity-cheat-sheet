# Основы Camera

Компонент `Camera` определяет, что и как рендерится на экране. Основные свойства:

| Свойство | Описание |
|----------|----------|
| `Field of View` | Угол обзора (только Perspective) |
| `Size` | Полуразмер вьюпорта (только Orthographic) |
| `Near Clip Plane` | Минимальная дистанция рендеринга |
| `Far Clip Plane` | Максимальная дистанция рендеринга |
| `Depth` | Порядок рендеринга (больше = поверх) |

```csharp
Camera cam = Camera.main;

// Изменение FOV (например, эффект прицеливания)
cam.fieldOfView = 40f;

// Изменение дистанции отсечения
cam.nearClipPlane = 0.1f;
cam.farClipPlane  = 500f;
```

## Perspective vs Orthographic

```csharp
// Перспективная проекция — объекты вдали кажутся меньше (3D-игры)
cam.orthographic = false;
cam.fieldOfView  = 60f;

// Ортографическая проекция — нет перспективного искажения (2D-игры, UI)
cam.orthographic     = true;
cam.orthographicSize = 5f; // половина высоты вьюпорта в Unity-единицах
```

## Несколько камер и порядок рендеринга

При наличии нескольких камер порядок рендеринга определяется значением **Depth**: камера с большим Depth рисуется поверх.

```csharp
// Камера фона (skybox), рендерится первой
Camera bgCamera = ...;
bgCamera.depth      = -1;
bgCamera.clearFlags = CameraClearFlags.Skybox;

// Основная игровая камера
Camera mainCamera = ...;
mainCamera.depth      = 0;
mainCamera.clearFlags = CameraClearFlags.Skybox;

// Камера UI, рендерится поверх всего
Camera uiCamera = ...;
uiCamera.depth      = 1;
uiCamera.clearFlags = CameraClearFlags.Depth; // не затирает фон
```

**Сплит-скрин через Viewport Rect:**

```csharp
// Левая половина экрана (x=0, y=0, w=0.5, h=1)
cameraP1.rect = new Rect(0f, 0f, 0.5f, 1f);

// Правая половина экрана
cameraP2.rect = new Rect(0.5f, 0f, 0.5f, 1f);
```

## Culling Mask

Определяет, объекты каких **слоёв (Layer)** видит камера.

```csharp
// Показывать только слои Default и Player
cam.cullingMask = LayerMask.GetMask("Default", "Player");

// Скрыть конкретный слой (убрать бит)
int layer = LayerMask.NameToLayer("Enemy");
cam.cullingMask &= ~(1 << layer);

// Показать конкретный слой (добавить бит)
cam.cullingMask |= (1 << layer);

// Показывать всё
cam.cullingMask = ~0;

// Не показывать ничего
cam.cullingMask = 0;
```

## Clear Flags

Определяет, чем камера заполняет экран перед рендерингом кадра.

| Режим | Описание |
|-------|----------|
| `Skybox` | Скайбокс (по умолчанию для 3D) |
| `SolidColor` | Сплошной цвет (`backgroundColor`) |
| `DepthOnly` | Очищает только буфер глубины, фон не трогает — для наложения поверх другой камеры |
| `Nothing` | Ничего не очищает — артефакты предыдущего кадра остаются |

```csharp
// Сплошной цвет фона
cam.clearFlags       = CameraClearFlags.SolidColor;
cam.backgroundColor  = new Color(0.1f, 0.1f, 0.1f, 1f);

// Только глубина — для UI-камеры поверх игровой
cam.clearFlags = CameraClearFlags.Depth;
```
