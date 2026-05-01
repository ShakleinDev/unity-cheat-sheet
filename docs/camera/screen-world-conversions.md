# Конвертация координат

## ScreenToWorldPoint и WorldToScreenPoint

```csharp
Camera cam = Camera.main;

// Экранные координаты → мировые
// Экран: (0,0) — нижний левый угол, (Screen.width, Screen.height) — верхний правый
// Z — расстояние от камеры до точки в мировом пространстве
Vector3 screenPos = new Vector3(Screen.width / 2f, Screen.height / 2f, 10f);
Vector3 worldPos  = cam.ScreenToWorldPoint(screenPos);

// Мировые координаты → экранные
Vector3 enemyWorldPos  = enemy.transform.position;
Vector3 enemyScreenPos = cam.WorldToScreenPoint(enemyWorldPos);

// Проверка, находится ли объект перед камерой
bool isInFront = enemyScreenPos.z > 0f;

// Проверка, находится ли объект в пределах экрана
bool isOnScreen = enemyScreenPos.z > 0f
               && enemyScreenPos.x > 0f && enemyScreenPos.x < Screen.width
               && enemyScreenPos.y > 0f && enemyScreenPos.y < Screen.height;
```

## ScreenPointToRay

Создаёт луч из позиции на экране в мировое пространство — основа для кликов мышью.

```csharp
// Луч из позиции мыши
Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);

if (Physics.Raycast(ray, out RaycastHit hit, maxDistance: 100f))
{
    Debug.Log($"Попали в: {hit.collider.name}");
    Debug.DrawLine(ray.origin, hit.point, Color.red, 2f);
}

// ViewportPointToRay — то же, но координаты в диапазоне 0..1
// (0.5, 0.5) — центр экрана
Ray centerRay = Camera.main.ViewportPointToRay(new Vector3(0.5f, 0.5f, 0f));
```

## Практические примеры

### Клик мышью на объект в мире

```csharp
public class ClickSelector : MonoBehaviour
{
    [SerializeField] LayerMask _selectableLayers;

    void Update()
    {
        if (!Input.GetMouseButtonDown(0)) return;

        Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);

        if (Physics.Raycast(ray, out RaycastHit hit, 200f, _selectableLayers))
            hit.collider.GetComponent<ISelectable>()?.Select();
    }
}
```

### UI-маркер над головой 3D-персонажа

```csharp
public class WorldSpaceMarker : MonoBehaviour
{
    [SerializeField] Transform  _target;       // 3D-объект в мире
    [SerializeField] RectTransform _marker;    // UI-элемент
    [SerializeField] Canvas     _canvas;

    void LateUpdate()
    {
        Vector3 screenPos = Camera.main.WorldToScreenPoint(_target.position);

        // Скрыть маркер, если объект за камерой
        bool behind = screenPos.z < 0f;
        _marker.gameObject.SetActive(!behind);

        if (behind) return;

        // Конвертируем экранные координаты в координаты Canvas
        RectTransformUtility.ScreenPointToLocalPointInRectangle(
            _canvas.transform as RectTransform,
            screenPos,
            _canvas.renderMode == RenderMode.ScreenSpaceOverlay ? null : Camera.main,
            out Vector2 localPoint
        );

        _marker.localPosition = localPoint;
    }
}
```

### Перемещение объекта в позицию клика (вид сверху)

```csharp
public class ClickToMove : MonoBehaviour
{
    [SerializeField] LayerMask _groundLayer;

    void Update()
    {
        if (!Input.GetMouseButtonDown(1)) return;

        Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);

        if (Physics.Raycast(ray, out RaycastHit hit, 300f, _groundLayer))
            GetComponent<NavMeshAgent>().SetDestination(hit.point);
    }
}
```
