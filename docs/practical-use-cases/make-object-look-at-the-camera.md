# Unity Billboard - Поворот объекта к камере

Удерживайте UI-элементы или билборды повёрнутыми к активной камере, чтобы они всегда оставались читаемыми и видимыми. `Quaternion.LookRotation` переориентирует вектор вперёд в сторону камеры с возможностью блокировки определённой оси.

## Поведение

1. Сохраните ссылку на трансформ камеры, к которой нужно поворачиваться.
2. Вычислите направление от объекта к камере.
3. Обнулите оси, которые нужно заблокировать (например, чтобы билборды оставались вертикальными).
4. Используйте `Quaternion.LookRotation` для ориентации объекта в нужном направлении.
5. Обновляйте в `LateUpdate`, чтобы поворот происходил после движения камеры.

## Пример

```csharp
using UnityEngine;

public class BillboardToCamera : MonoBehaviour
{
    [SerializeField] private Transform cameraTransform;
    [SerializeField] private bool lockY = true; // сохранять вертикальное положение для UI в мировом пространстве

    private void Awake()
    {
        if (!cameraTransform)
        {
            Camera mainCam = Camera.main;
            cameraTransform = mainCam ? mainCam.transform : null;
        }
    }

    private void LateUpdate()
    {
        if (!cameraTransform) return;

        Vector3 direction = cameraTransform.position - transform.position;

        if (lockY)
        {
            direction.y = 0f;
            if (direction.sqrMagnitude < 0.0001f)
            {
                return; // избежать некорректного поворота
            }
        }

        transform.rotation = Quaternion.LookRotation(direction, Vector3.up);
    }
}
```

### Примечания

- Для UI-канвасов убедитесь, что режим рендеринга канваса — **World Space** или **Camera**, чтобы увидеть эффект.
- Блокировка оси Y предотвращает наклон объекта при крутых углах камеры — отключите её для эффектов, которые должны полностью отслеживать камеру.
- Если существует несколько билбордов, кэшируйте трансформ камеры в общем менеджере, чтобы избежать повторных вызовов `Camera.main`.
- Используйте `transform.LookAt(cameraTransform)` как краткую запись, если блокировка оси не нужна.
