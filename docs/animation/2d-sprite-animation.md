# 2D Sprite Animation

## Создание Sprite Sheet и нарезка спрайтов

1. Импортировать изображение в проект
2. В инспекторе установить **Texture Type** → `Sprite (2D and UI)`
3. Установить **Sprite Mode** → `Multiple`
4. Нажать **Sprite Editor** → *Slice* → выбрать режим нарезки:
   - `Automatic` — Unity найдёт спрайты по прозрачности
   - `Grid By Cell Size` — нарезка по размеру ячейки в пикселях
   - `Grid By Cell Count` — нарезка по количеству строк и столбцов
5. Нажать **Apply**

## Создание Animation Clip

**Способ 1 — через окно Animation:**
1. Выбрать GameObject с компонентом `Sprite Renderer`
2. Открыть *Window → Animation → Animation*
3. Нажать **Create** — создастся `.anim` файл и Animator Controller
4. Перетащить нужные спрайты из Project на временную шкалу

**Способ 2 — автоматически из спрайтов:**
1. Выделить нужные спрайты в папке Project
2. Перетащить их прямо на GameObject в сцене
3. Unity предложит сохранить `.anim` файл — согласиться

```csharp
// Рекомендуемая частота кадров для пиксельных игр
// В окне Animation: Samples = 12 (или 8/24 в зависимости от стиля)
```

## Настройка Animator Controller

Типичная структура для 2D-платформера:

```
Base Layer
  ├─ Idle        (зациклен)
  ├─ Run         (зациклен)
  ├─ Jump        (не зациклен)
  └─ Fall        (зациклен)

Параметры:
  Speed    : Float
  IsGrounded : Bool
  Jump     : Trigger
```

Переходы:
- `Idle → Run`: Speed > 0.1
- `Run → Idle`: Speed < 0.1
- `Any State → Jump`: Trigger Jump
- `Jump → Fall`: normalizedTime > 0.9 (Has Exit Time)
- `Fall → Idle`: IsGrounded = true

## Управление из кода

```csharp
public class PlayerAnimation2D : MonoBehaviour
{
    [SerializeField] float _runThreshold = 0.1f;

    Animator     _animator;
    Rigidbody2D  _rb;
    SpriteRenderer _spriteRenderer;

    static readonly int SpeedHash      = Animator.StringToHash("Speed");
    static readonly int IsGroundedHash = Animator.StringToHash("IsGrounded");
    static readonly int JumpHash       = Animator.StringToHash("Jump");

    void Awake()
    {
        _animator        = GetComponent<Animator>();
        _rb              = GetComponent<Rigidbody2D>();
        _spriteRenderer  = GetComponent<SpriteRenderer>();
    }

    void Update()
    {
        float speed = Mathf.Abs(_rb.linearVelocity.x);
        _animator.SetFloat(SpeedHash, speed);
        _animator.SetBool(IsGroundedHash, IsGrounded());

        // Отражение спрайта по горизонтали
        if (_rb.linearVelocity.x > _runThreshold)
            _spriteRenderer.flipX = false;
        else if (_rb.linearVelocity.x < -_runThreshold)
            _spriteRenderer.flipX = true;
    }

    public void TriggerJump() => _animator.SetTrigger(JumpHash);

    bool IsGrounded() =>
        Physics2D.Raycast(transform.position, Vector2.down, 0.1f);
}
```

## Советы по оптимизации

```csharp
// Кэшировать хэши параметров в static readonly полях
// вместо передачи строк в каждом кадре
static readonly int SpeedHash = Animator.StringToHash("Speed");

// НЕ делать так каждый кадр — строка хэшируется каждый раз
_animator.SetFloat("Speed", speed); // медленнее

// Делать так
_animator.SetFloat(SpeedHash, speed); // быстрее
```
