# Blend Trees

Blend Tree позволяет плавно смешивать несколько анимационных клипов на основе одного или двух числовых параметров. Используется вместо множества переходов там, где нужна плавность.

## 1D Blend Tree

Смешивание по одному параметру (например, скорость → idle / walk / run).

**Настройка в редакторе:**
1. Создать состояние → ПКМ → *Create State → From New Blend Tree*
2. Выбрать параметр (например, `Speed`)
3. Добавить мотионы (клипы) и задать пороговые значения

```
Speed = 0.0  →  Idle
Speed = 0.5  →  Walk
Speed = 1.0  →  Run
```

```csharp
void Update()
{
    float speed = _rigidbody.linearVelocity.magnitude;

    // Нормализуем, например, от 0 до maxSpeed → 0..1
    float normalized = Mathf.Clamp01(speed / maxSpeed);
    _animator.SetFloat("Speed", normalized);

    // Для плавности используем сглаживание
    float current = _animator.GetFloat("Speed");
    _animator.SetFloat("Speed", Mathf.Lerp(current, normalized, Time.deltaTime * 10f));
}
```

## 2D Blend Tree

Смешивание по двум параметрам (например, движение вперёд/назад и вправо/влево).

**Типы 2D Blend Tree:**
| Тип | Когда использовать |
|-----|--------------------|
| `2D Simple Directional` | Движение в разные стороны (нет двух клипов в одном направлении) |
| `2D Freeform Directional` | То же, но с клипом в центре (стоять на месте) |
| `2D Freeform Cartesian` | Независимые параметры (например, скорость + наклон) |

```csharp
void Update()
{
    float h = Input.GetAxis("Horizontal"); // влево/вправо
    float v = Input.GetAxis("Vertical");   // вперёд/назад

    _animator.SetFloat("VelocityX", h);
    _animator.SetFloat("VelocityZ", v);
}
```

## Настройка весов и порогов

- **Порог (Threshold)** — значение параметра, при котором данный клип имеет вес 1.0
- **Automate Thresholds** — Unity расставит пороги равномерно автоматически
- **Compute Thresholds** — Unity вычислит пороги из скорости клипа (для motion-based)

```csharp
// Плавное изменение параметра через Lerp предотвращает резкие переключения
void SetAnimationSpeed(float targetSpeed)
{
    float current = _animator.GetFloat("Speed");
    float smoothed = Mathf.MoveTowards(current, targetSpeed, Time.deltaTime * 5f);
    _animator.SetFloat("Speed", smoothed);
}
```
