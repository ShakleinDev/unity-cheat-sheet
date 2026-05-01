# Image и RawImage

## Image — отображение Sprite

`Image` используется для отображения спрайтов (Sprite) в UI. Поддерживает несколько режимов отрисовки.

```csharp
using UnityEngine.UI;

Image img = GetComponent<Image>();

// Назначить спрайт
img.sprite = Resources.Load<Sprite>("Icons/sword");

// Цвет и прозрачность
img.color = Color.white;
img.color = new Color(1f, 1f, 1f, 0.5f); // полупрозрачный

// Включить / выключить raycast (влияет на клики)
img.raycastTarget = false;
```

## Режимы Image Type

```csharp
// Simple — масштабирует спрайт целиком (по умолчанию)
img.type = Image.Type.Simple;
img.preserveAspect = true; // сохранить пропорции

// Sliced — 9-нарезка (для кнопок с закруглёнными углами)
img.type = Image.Type.Sliced;
img.pixelsPerUnitMultiplier = 1f;

// Tiled — повторяет текстуру
img.type = Image.Type.Tiled;

// Filled — заполняет от 0 до 1 (HP-бары, таймеры)
img.type = Image.Type.Filled;
img.fillMethod = Image.FillMethod.Horizontal;
img.fillOrigin = (int)Image.OriginHorizontal.Left;
img.fillAmount = 0.75f; // 75% заполнения
```

## HP-бар через fillAmount

```csharp
[SerializeField] Image hpBar;

void UpdateHpBar(float current, float max)
{
    hpBar.fillAmount = Mathf.Clamp01(current / max);
}
```

## Радиальный таймер

```csharp
[SerializeField] Image timerImage;

IEnumerator RadialTimer(float duration)
{
    timerImage.type = Image.Type.Filled;
    timerImage.fillMethod = Image.FillMethod.Radial360;
    timerImage.fillClockwise = false;

    float elapsed = 0f;
    while (elapsed < duration)
    {
        elapsed += Time.deltaTime;
        timerImage.fillAmount = 1f - (elapsed / duration);
        yield return null;
    }
    timerImage.fillAmount = 0f;
}
```

## Смена спрайта во время игры

```csharp
[SerializeField] Image icon;
[SerializeField] Sprite[] itemSprites;

void SetIcon(int index)
{
    icon.sprite = itemSprites[index];
    // Подогнать размер RectTransform под спрайт
    icon.SetNativeSize();
}
```

## RawImage — отображение Texture2D / RenderTexture

`RawImage` работает с любой `Texture`, а не только со спрайтами. Удобен для RenderTexture (мини-карта, портрет персонажа).

```csharp
using UnityEngine.UI;

RawImage rawImg = GetComponent<RawImage>();

// Назначить текстуру
rawImg.texture = Resources.Load<Texture2D>("Textures/background");

// Назначить RenderTexture (например, с камеры)
rawImg.texture = renderTexture;

// UV-прямоугольник — для отображения части текстуры или скроллинга
rawImg.uvRect = new Rect(0f, 0f, 1f, 1f);   // полная текстура
rawImg.uvRect = new Rect(0.25f, 0f, 0.5f, 1f); // центральная половина
```

## Скроллинг фона через uvRect

```csharp
[SerializeField] RawImage background;
[SerializeField] float scrollSpeed = 0.1f;

void Update()
{
    Rect r = background.uvRect;
    r.x += scrollSpeed * Time.deltaTime;
    background.uvRect = r;
}
```

## Рекомендации

- Используйте **Image** для спрайтов/иконок, **RawImage** — для произвольных текстур и RenderTexture.
- Отключайте `raycastTarget` у декоративных элементов — это снизит нагрузку на GraphicRaycaster.
- Для HP-баров и таймеров предпочитайте `Image.Type.Filled` вместо масштабирования RectTransform — корректнее с 9-нарезкой.
- `SetNativeSize()` удобен после смены спрайта в инвентаре — автоматически подгоняет размер.
