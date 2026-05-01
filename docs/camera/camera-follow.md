# Следование камеры

## Простое следование с Lerp и SmoothDamp

```csharp
public class CameraFollow : MonoBehaviour
{
    [SerializeField] Transform _target;
    [SerializeField] Vector3   _offset = new Vector3(0f, 5f, -10f);

    // --- Lerp ---
    [SerializeField] float _lerpSpeed = 5f;

    void LateUpdate()
    {
        Vector3 desiredPos = _target.position + _offset;
        transform.position = Vector3.Lerp(transform.position, desiredPos, _lerpSpeed * Time.deltaTime);
    }
}
```

```csharp
// --- SmoothDamp (более физически корректно, с контролем скорости) ---
public class CameraFollowSmooth : MonoBehaviour
{
    [SerializeField] Transform _target;
    [SerializeField] Vector3   _offset      = new Vector3(0f, 5f, -10f);
    [SerializeField] float     _smoothTime  = 0.3f;

    Vector3 _velocity = Vector3.zero;

    void LateUpdate()
    {
        Vector3 desiredPos = _target.position + _offset;
        transform.position = Vector3.SmoothDamp(
            transform.position, desiredPos, ref _velocity, _smoothTime
        );
    }
}
```

> ✅ `LateUpdate` гарантирует, что камера обновляется после движения персонажа — это устраняет дёргание.

## Следование с поворотом

```csharp
public class CameraFollowAndLook : MonoBehaviour
{
    [SerializeField] Transform _target;
    [SerializeField] Vector3   _offset     = new Vector3(0f, 3f, -8f);
    [SerializeField] float     _posSmooth  = 0.25f;
    [SerializeField] float     _rotSpeed   = 5f;

    Vector3 _velocity;

    void LateUpdate()
    {
        // Следование за позицией
        Vector3 desired = _target.position + _target.TransformDirection(_offset);
        transform.position = Vector3.SmoothDamp(transform.position, desired, ref _velocity, _posSmooth);

        // Плавный поворот в сторону цели
        Quaternion lookRot = Quaternion.LookRotation(_target.position - transform.position);
        transform.rotation = Quaternion.Slerp(transform.rotation, lookRot, _rotSpeed * Time.deltaTime);
    }
}
```

## Ограничение зоны слежения

```csharp
public class CameraFollowBounded : MonoBehaviour
{
    [SerializeField] Transform _target;
    [SerializeField] Vector3   _offset;

    [Header("Границы мира")]
    [SerializeField] float _minX, _maxX;
    [SerializeField] float _minY, _maxY;

    void LateUpdate()
    {
        Vector3 desired = _target.position + _offset;

        // Ограничиваем позицию камеры в пределах уровня
        desired.x = Mathf.Clamp(desired.x, _minX, _maxX);
        desired.y = Mathf.Clamp(desired.y, _minY, _maxY);

        transform.position = desired;
    }
}
```

```csharp
// Ограничение через Bounds (удобно для 2D-уровней)
[SerializeField] Bounds _cameraBounds;

void LateUpdate()
{
    Vector3 pos = _target.position + _offset;

    float halfH = Camera.main.orthographicSize;
    float halfW = halfH * Camera.main.aspect;

    pos.x = Mathf.Clamp(pos.x, _cameraBounds.min.x + halfW, _cameraBounds.max.x - halfW);
    pos.y = Mathf.Clamp(pos.y, _cameraBounds.min.y + halfH, _cameraBounds.max.y - halfH);

    transform.position = pos;
}
```

## Look Ahead

Камера смещается чуть вперёд по направлению движения — игрок видит больше того, куда движется.

```csharp
public class CameraLookAhead : MonoBehaviour
{
    [SerializeField] Transform _target;
    [SerializeField] Vector3   _offset;
    [SerializeField] float     _lookAheadDistance = 3f;
    [SerializeField] float     _smoothTime        = 0.3f;

    Vector3 _velocity;
    Vector3 _previousTargetPos;

    void LateUpdate()
    {
        // Направление движения цели
        Vector3 moveDir = (_target.position - _previousTargetPos).normalized;
        _previousTargetPos = _target.position;

        // Смещение камеры вперёд по направлению движения
        Vector3 lookAheadOffset = moveDir * _lookAheadDistance;
        Vector3 desired = _target.position + _offset + lookAheadOffset;

        transform.position = Vector3.SmoothDamp(
            transform.position, desired, ref _velocity, _smoothTime
        );
    }
}
```
