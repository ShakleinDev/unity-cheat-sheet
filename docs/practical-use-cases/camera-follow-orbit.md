# Unity — Следование камеры и орбитальное вращение (камера от третьего лица)

Плавно следуйте за целью, позволяя мыши или геймпаду вращать камеру вокруг неё. Используйте `Vector3.SmoothDamp` для плавного отслеживания позиции и `Quaternion.AngleAxis` для накопления углов поворота по горизонтали (yaw) и вертикали (pitch).

## Поведение

1. Считываем входные данные для орбиты (ось X/Y мыши, правый стик или произвольные оси).
2. Накапливаем yaw и pitch, ограничивая pitch, чтобы камера не переворачивалась.
3. Преобразуем пару yaw/pitch во вращение с помощью `Quaternion.AngleAxis`.
4. Применяем вращение к вектору смещения камеры, получая желаемую позицию.
5. Плавно перемещаем камеру к этой позиции с помощью `Vector3.SmoothDamp`.
6. Поворачиваем камеру обратно к цели, чтобы она всегда смотрела на неё.

## Пример

```csharp
using UnityEngine;

public class OrbitFollowCamera : MonoBehaviour
{
    [SerializeField] private Transform target;
    [SerializeField] private Vector3 followOffset = new Vector3(0f, 3f, -6f);
    [SerializeField] private float followSmoothTime = 0.2f;
    [SerializeField] private float orbitSpeed = 120f;
    [SerializeField] private Vector2 pitchLimits = new Vector2(-30f, 60f);

    private Vector3 velocity;    // переиспользуется в SmoothDamp
    private float yaw;           // вращение вокруг глобальной оси Y (орбита влево/вправо)
    private float pitch;         // вращение вокруг локальной оси X (орбита вверх/вниз)

    private void Awake()
    {
        if (!target)
        {
            enabled = false;
            Debug.LogWarning("OrbitFollowCamera отключена: цель не назначена.", this);
            return;
        }

        Vector3 toTarget = target.position - transform.position;
        Quaternion initialLook = Quaternion.LookRotation(toTarget, Vector3.up);
        yaw = initialLook.eulerAngles.y;
        pitch = initialLook.eulerAngles.x;
    }

    private void LateUpdate()
    {
        Vector2 orbitInput = new Vector2(
            Input.GetAxis("Mouse X") + Input.GetAxis("CameraHorizontal"),
            Input.GetAxis("Mouse Y") + Input.GetAxis("CameraVertical")
        );

        yaw += orbitInput.x * orbitSpeed * Time.deltaTime;
        pitch -= orbitInput.y * orbitSpeed * Time.deltaTime;
        pitch = Mathf.Clamp(pitch, pitchLimits.x, pitchLimits.y);

        Quaternion orbitRotation = Quaternion.AngleAxis(yaw, Vector3.up) *
                                   Quaternion.AngleAxis(pitch, Vector3.right);

        Vector3 desiredPosition = target.position + orbitRotation * followOffset;
        transform.position = Vector3.SmoothDamp(transform.position, desiredPosition, ref velocity, followSmoothTime);
        transform.rotation = Quaternion.LookRotation(target.position - transform.position, Vector3.up);
    }
}
```

### Примечания

- Оси `CameraHorizontal` / `CameraVertical` — опциональные оси правого стика; удалите их, если используете только мышь.
- Регулируйте отзывчивость орбиты, изменяя `orbitSpeed` и `followSmoothTime`.
- Изменение `followOffset` в режиме игры позволяет почувствовать, как ведут себя разные позиции камеры (вид через плечо vs. вид сверху).
- Используйте `LateUpdate` для слежения за целями, которые двигаются в `Update`, — это гарантирует, что камера реагирует после того, как персонаж уже переместился.
- Замените `Vector3.SmoothDamp` на `Vector3.MoveTowards`, если хотите мгновенное переключение без сглаживания.
