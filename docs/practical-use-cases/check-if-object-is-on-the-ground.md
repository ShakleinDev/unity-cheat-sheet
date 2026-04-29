# Unity Ground Check - Определение IsGrounded

Определяет, находится ли персонаж или объект на земле, чтобы разрешить прыжок, воспроизвести VFX приземления или переключить режим передвижения. Луч вниз от объекта — надёжный подход при использовании фильтрации по слоям.

## Поведение

1. Пустить луч вниз из позиции объекта (или чуть выше ног).
2. Ограничить длину луча расстоянием от точки испускания до земли плюс небольшой запас.
3. Использовать маску слоёв, чтобы попадать только по проходимым поверхностям и игнорировать триггеры.
4. Считать успешное попадание признаком «на земле» и при необходимости сохранять данные попадания для поверхностных эффектов.

## Пример

```csharp
using UnityEngine;

public class GroundedCheck : MonoBehaviour
{
    [SerializeField] private float groundCheckDistance = 0.6f;
    [SerializeField] private LayerMask groundLayers;

    public bool IsGrounded { get; private set; }
    public RaycastHit GroundHit { get; private set; }

    private void Update()
    {
        Vector3 origin = transform.position + Vector3.up * 0.1f; // немного поднять, чтобы избежать самопересечения
        IsGrounded = Physics.Raycast(origin, Vector3.down, out RaycastHit hit, groundCheckDistance, groundLayers, QueryTriggerInteraction.Ignore);

        if (IsGrounded)
        {
            GroundHit = hit;
        }
    }
}
```

### Примечания

- Настройте `groundCheckDistance` под высоту коллайдера или смещение ног; слишком короткое значение может не замечать склоны, слишком длинное — ложно определять нижние платформы.
- Для персонажей на основе капсулы сочетайте этот рейкаст с `CharacterController.isGrounded` для повышенной надёжности.
- Используйте `GroundHit.normal`, чтобы выравнивать шаги по поверхности или определять крутые склоны перед разрешением прыжка.
- Замените `Physics.Raycast` на `Physics.SphereCast`, если нужна более широкая зона обнаружения для неровного рельефа.
