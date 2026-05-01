# Animation Events

Animation Events позволяют вызывать методы скрипта в точно заданный момент времени анимационного клипа — например, воспроизвести звук шага, создать эффект или нанести урон.

## Добавление события в клип

1. Открыть окно **Animation** (*Window → Animation → Animation*)
2. Выбрать нужный клип
3. Переместить ползунок на нужный кадр
4. Нажать кнопку **Add Event** (значок флажка) на временной шкале
5. В инспекторе события выбрать метод из выпадающего списка

> ⚠️ Метод должен находиться в скрипте на **том же GameObject**, что и компонент `Animator`.

## Привязка к методу скрипта

```csharp
public class PlayerAnimationEvents : MonoBehaviour
{
    [SerializeField] AudioSource _audioSource;
    [SerializeField] AudioClip   _footstepClip;
    [SerializeField] ParticleSystem _dustEffect;

    // Вызывается из события анимации шага
    void OnFootstep()
    {
        _audioSource.PlayOneShot(_footstepClip);
        _dustEffect.Play();
    }

    // Вызывается в момент удара оружием
    void OnAttackHit()
    {
        // Проверка попадания в этот момент анимации
        Collider[] hits = Physics.OverlapSphere(
            transform.position + transform.forward,
            attackRadius
        );
        foreach (var hit in hits)
            hit.GetComponent<IDamageable>()?.TakeDamage(damage);
    }
}
```

## Передача параметров

В одно событие можно передать **один** параметр одного из следующих типов:

| Тип | Поле в инспекторе |
|-----|-------------------|
| `float` | Float |
| `int` | Int |
| `string` | String |
| `UnityEngine.Object` | Object |

```csharp
// Метод принимает float — например, громкость шага
void OnFootstep(float volume)
{
    _audioSource.volume = volume;
    _audioSource.PlayOneShot(_footstepClip);
}

// Метод принимает string — имя эффекта
void OnPlayEffect(string effectName)
{
    var effect = EffectManager.Instance.Get(effectName);
    effect?.Play();
}

// Метод принимает Object — конкретный ассет
void OnPlaySound(UnityEngine.Object clip)
{
    _audioSource.PlayOneShot(clip as AudioClip);
}
```

## Практические примеры

### Звук шагов
```csharp
// В анимации ходьбы: два события на кадры касания левой и правой ноги
void OnFootstepLeft()  => PlayFootstep(_leftFootClip);
void OnFootstepRight() => PlayFootstep(_rightFootClip);

void PlayFootstep(AudioClip clip)
{
    // Небольшой разброс высоты тона для естественности
    _audioSource.pitch = Random.Range(0.9f, 1.1f);
    _audioSource.PlayOneShot(clip);
}
```

### Нанесение урона в нужный момент удара
```csharp
// Событие ставится ровно на кадр контакта оружия с целью
void OnDealDamage()
{
    if (_currentTarget != null && _currentTarget.IsAlive)
        _currentTarget.TakeDamage(_damage);
}
```

### Спавн снаряда в момент броска
```csharp
void OnThrowProjectile()
{
    Instantiate(_projectilePrefab, _throwPoint.position, _throwPoint.rotation);
}
```
