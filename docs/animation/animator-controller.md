# Animator Controller

Animator Controller — это граф состояний, управляющий воспроизведением анимационных клипов на объекте. Он хранится как ассет `.controller` и назначается компоненту `Animator`.

## Параметры

Параметры — это переменные, через которые скрипт управляет переходами между состояниями.

| Тип | Описание |
|-----|----------|
| `Bool` | Включён / выключен |
| `Int` | Целое число |
| `Float` | Дробное число |
| `Trigger` | Одноразовый импульс, сбрасывается автоматически после срабатывания |

```csharp
Animator animator = GetComponent<Animator>();

// Установка параметров
animator.SetBool("IsRunning", true);
animator.SetFloat("Speed", 5.5f);
animator.SetInteger("WeaponType", 2);
animator.SetTrigger("Jump");

// Чтение параметров
bool isRunning = animator.GetBool("IsRunning");
float speed    = animator.GetFloat("Speed");
```

## Состояния и переходы

**Состояние (State)** — один анимационный клип или Blend Tree.  
**Переход (Transition)** — стрелка между состояниями с условиями срабатывания.

Важные настройки перехода:
- **Has Exit Time** — ждать окончания текущей анимации перед переходом. Отключайте для мгновенной реакции (например, прыжок по нажатию кнопки).
- **Transition Duration** — длительность плавного смешивания двух состояний.
- **Conditions** — список условий (параметр + оператор + значение).

```csharp
// Пример: персонаж начинает бежать
animator.SetFloat("Speed", GetComponent<Rigidbody>().linearVelocity.magnitude);
```

## Управление из кода

```csharp
public class PlayerAnimator : MonoBehaviour
{
    Animator _animator;

    void Awake() => _animator = GetComponent<Animator>();

    void Update()
    {
        float speed = GetComponent<Rigidbody>().linearVelocity.magnitude;
        _animator.SetFloat("Speed", speed);
        _animator.SetBool("IsGrounded", IsGrounded());
    }

    public void TriggerAttack() => _animator.SetTrigger("Attack");
    public void TriggerJump()   => _animator.SetTrigger("Jump");

    bool IsGrounded() =>
        Physics.Raycast(transform.position, Vector3.down, 0.1f);
}
```

## Получение текущего состояния

```csharp
AnimatorStateInfo stateInfo = animator.GetCurrentAnimatorStateInfo(0); // 0 — индекс слоя

// Проверка по имени
if (stateInfo.IsName("Run"))
{
    Debug.Log("Персонаж бежит");
}

// Проверка по хэшу (быстрее, рекомендуется)
int runHash = Animator.StringToHash("Run");
if (stateInfo.shortNameHash == runHash)
{
    Debug.Log("Персонаж бежит");
}

// Прогресс воспроизведения (0.0 — начало, 1.0 — конец)
float progress = stateInfo.normalizedTime;

// Проверка, завершилась ли анимация (для не-зацикленных клипов)
bool finished = stateInfo.normalizedTime >= 1f;
```
