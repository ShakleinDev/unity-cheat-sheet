# Тряска камеры

## Тряска через корутину

```csharp
public class CameraShake : MonoBehaviour
{
    Vector3 _originPosition;

    // Запустить тряску
    public void Shake(float duration, float magnitude)
    {
        StopAllCoroutines(); // остановить предыдущую тряску, если идёт
        StartCoroutine(ShakeRoutine(duration, magnitude));
    }

    IEnumerator ShakeRoutine(float duration, float magnitude)
    {
        _originPosition = transform.localPosition;
        float elapsed = 0f;

        while (elapsed < duration)
        {
            elapsed += Time.deltaTime;

            // Затухание интенсивности к концу
            float strength = Mathf.Lerp(magnitude, 0f, elapsed / duration);

            transform.localPosition = _originPosition + (Vector3)Random.insideUnitCircle * strength;

            yield return null;
        }

        transform.localPosition = _originPosition;
    }
}
```

```csharp
// Использование
[SerializeField] CameraShake _cameraShake;

void OnExplosion()
{
    _cameraShake.Shake(duration: 0.4f, magnitude: 0.3f);
}
```

## Перлин-шум для плавной тряски

Случайные значения дают дёрганую тряску. Перлин-шум даёт плавную, волнообразную — больше подходит для длительных эффектов (двигатель, землетрясение).

```csharp
public class CameraShakePerlin : MonoBehaviour
{
    [SerializeField] float _trauma;        // текущая интенсивность (0..1)
    [SerializeField] float _traumaDecay  = 1.5f;  // скорость затухания
    [SerializeField] float _maxShakePos  = 0.3f;
    [SerializeField] float _maxShakeRot  = 3f;
    [SerializeField] float _frequency   = 20f;    // частота шума

    Vector3    _originPos;
    Quaternion _originRot;
    float      _seed;

    void Awake()
    {
        _originPos = transform.localPosition;
        _originRot = transform.localRotation;
        _seed      = Random.value * 100f; // уникальный сдвиг для каждой камеры
    }

    // Добавить травму (накапливается)
    public void AddTrauma(float amount)
    {
        _trauma = Mathf.Clamp01(_trauma + amount);
    }

    void Update()
    {
        if (_trauma <= 0f) return;

        float shake = _trauma * _trauma; // квадрат — более резкое затухание

        // Смещение через Перлин-шум (разные сиды для X и Y)
        float offsetX = (Mathf.PerlinNoise(_seed + 0f, Time.time * _frequency) * 2f - 1f) * _maxShakePos * shake;
        float offsetY = (Mathf.PerlinNoise(_seed + 1f, Time.time * _frequency) * 2f - 1f) * _maxShakePos * shake;
        float rotZ    = (Mathf.PerlinNoise(_seed + 2f, Time.time * _frequency) * 2f - 1f) * _maxShakeRot  * shake;

        transform.localPosition = _originPos + new Vector3(offsetX, offsetY, 0f);
        transform.localRotation = _originRot * Quaternion.Euler(0f, 0f, rotZ);

        // Затухание
        _trauma = Mathf.Max(0f, _trauma - _traumaDecay * Time.deltaTime);
    }
}
```

```csharp
// Использование — накапливаемая тряска
[SerializeField] CameraShakePerlin _shake;

void OnBulletHit()    => _shake.AddTrauma(0.2f);
void OnExplosion()    => _shake.AddTrauma(0.6f);
void OnNuclearBomb()  => _shake.AddTrauma(1.0f);
```

> ✅ Модель с «травмой» (trauma) удобна тем, что несколько событий накапливаются естественно — большой взрыв рядом с маленьким дают соразмерный эффект.
