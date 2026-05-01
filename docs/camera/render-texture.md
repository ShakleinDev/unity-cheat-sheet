# Render Texture

**Render Texture** — текстура, в которую камера рендерит кадр вместо экрана. Результат можно использовать как обычную текстуру — на материале, в UI, в шейдере.

## Назначение камере

**Через редактор:**
1. *Assets → Create → Render Texture* — создать ассет
2. Выбрать нужное разрешение (например, 512×512 или 1920×1080)
3. Перетащить ассет в поле **Target Texture** компонента `Camera`

**Через код:**

```csharp
// Назначить готовый ассет
[SerializeField] RenderTexture _renderTexture;
[SerializeField] Camera        _renderCamera;

void Awake()
{
    _renderCamera.targetTexture = _renderTexture;
}

// Убрать — камера снова рендерит на экран
_renderCamera.targetTexture = null;
```

## Отображение в UI

```csharp
using UnityEngine.UI;

// Raw Image отображает RenderTexture как UI-элемент
[SerializeField] RawImage      _display;
[SerializeField] RenderTexture _renderTexture;

void Awake()
{
    _display.texture = _renderTexture;
}
```

## Практические примеры

### Мини-карта

```
Настройка:
  MinimapCamera
    ├─ Position: над сценой, смотрит вниз (Orthographic)
    ├─ Culling Mask: только слой "Minimap"
    └─ Target Texture: MinimapRT (256×256)

  UI Canvas
    └─ RawImage (MinimapRT) — в углу экрана
```

```csharp
public class MinimapCamera : MonoBehaviour
{
    [SerializeField] Transform _player;
    [SerializeField] float     _height = 50f;

    void LateUpdate()
    {
        Vector3 pos = _player.position;
        pos.y = _player.position.y + _height;
        transform.position = pos;
    }
}
```

### Монитор / экран безопасности

```csharp
// Камера наблюдения рендерит в RenderTexture
// RenderTexture назначена материалу объекта "монитор" в сцене
[SerializeField] Camera        _securityCamera;
[SerializeField] RenderTexture _cctv;
[SerializeField] Renderer      _monitor;

void Awake()
{
    _securityCamera.targetTexture = _cctv;
    _monitor.material.mainTexture = _cctv;
}
```

### Вид из прицела

```csharp
public class SniperScope : MonoBehaviour
{
    [SerializeField] Camera        _scopeCamera;
    [SerializeField] RenderTexture _scopeRT;
    [SerializeField] RawImage      _scopeDisplay;

    public void SetScopeActive(bool active)
    {
        _scopeCamera.enabled  = active;
        _scopeDisplay.enabled = active;
    }
}
```

## Создание в рантайме

```csharp
public class DynamicRenderTexture : MonoBehaviour
{
    [SerializeField] Camera    _cam;
    [SerializeField] RawImage  _display;

    RenderTexture _rt;

    void Start()
    {
        // Создать RenderTexture под текущее разрешение экрана
        _rt = new RenderTexture(Screen.width, Screen.height, depth: 24)
        {
            name            = "DynamicRT",
            filterMode      = FilterMode.Bilinear,
            antiAliasing    = 2
        };
        _rt.Create();

        _cam.targetTexture = _rt;
        _display.texture   = _rt;
    }

    void OnDestroy()
    {
        // Обязательно освободить ресурсы
        if (_rt != null)
        {
            _cam.targetTexture = null;
            _rt.Release();
            Destroy(_rt);
        }
    }
}
```

> ⚠️ Всегда вызывайте `Release()` и `Destroy()` для динамически созданных RenderTexture — иначе они остаются в памяти GPU до закрытия приложения.
