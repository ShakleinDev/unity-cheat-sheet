# Управление анимациями из кода

## Animator.Play() и Animator.CrossFade()

`Play()` — мгновенный переход в состояние.  
`CrossFade()` — плавное смешивание с переходом за указанное время.

```csharp
Animator _animator;

// Мгновенный переход
_animator.Play("Run");

// Переход с плавным смешиванием (0.2 сек)
_animator.CrossFade("Run", 0.2f);

// С указанием слоя и нормализованного времени старта
_animator.Play("Attack", 0, 0f);          // слой 0, начать с начала
_animator.CrossFade("Attack", 0.1f, 0);  // слой 0, переход 0.1 сек

// По хэшу (быстрее, чем по строке)
int runHash = Animator.StringToHash("Run");
_animator.CrossFade(runHash, 0.2f);
```

## Скорость и перемотка

```csharp
// Изменение глобальной скорости воспроизведения
_animator.speed = 1.5f;  // ускорить в 1.5 раза
_animator.speed = 0f;    // поставить на паузу
_animator.speed = 1f;    // вернуть нормальную скорость

// Перемотка: normalizedTime 0.0 = начало, 1.0 = конец
_animator.Play("Attack", 0, 0.5f); // начать с середины клипа

// Получить прогресс текущей анимации
float progress = _animator.GetCurrentAnimatorStateInfo(0).normalizedTime % 1f;
```

## Ожидание окончания анимации

```csharp
// Через корутину
IEnumerator PlayAndWait(string stateName)
{
    _animator.Play(stateName, 0, 0f);

    // Ждём один кадр, чтобы состояние успело обновиться
    yield return null;

    AnimatorStateInfo info = _animator.GetCurrentAnimatorStateInfo(0);

    // Ждём окончания (работает для не-зацикленных клипов)
    while (info.normalizedTime < 1f)
    {
        yield return null;
        info = _animator.GetCurrentAnimatorStateInfo(0);
    }

    Debug.Log($"{stateName} завершена");
}

// Использование
StartCoroutine(PlayAndWait("Death"));
```

```csharp
// Альтернатива через async/await
async System.Threading.Tasks.Task PlayAndWaitAsync(string stateName,
    System.Threading.CancellationToken ct = default)
{
    _animator.Play(stateName, 0, 0f);
    await System.Threading.Tasks.Task.Yield();

    while (_animator.GetCurrentAnimatorStateInfo(0).normalizedTime < 1f)
    {
        ct.ThrowIfCancellationRequested();
        await System.Threading.Tasks.Task.Yield();
    }
}
```

## AnimatorOverrideController

Позволяет менять анимационные клипы в рантайме без изменения структуры Animator Controller. Удобно для систем смены оружия, скинов или кастомных анимаций персонажа.

```csharp
[SerializeField] AnimatorOverrideController _overrideController;
[SerializeField] AnimationClip _swordAttackClip;
[SerializeField] AnimationClip _bowAttackClip;

Animator _animator;

void Awake()
{
    _animator = GetComponent<Animator>();

    // Назначаем Override Controller вместо оригинального
    _animator.runtimeAnimatorController = _overrideController;
}

// Заменить клип по имени оригинального клипа
public void EquipSword()
{
    _overrideController["Attack"] = _swordAttackClip;
}

public void EquipBow()
{
    _overrideController["Attack"] = _bowAttackClip;
}
```

```csharp
// Массовая замена клипов
void ApplyAnimationSet(AnimationClip[] clips, string[] originalNames)
{
    var overrides = new AnimationClipPair[clips.Length];

    for (int i = 0; i < clips.Length; i++)
    {
        overrides[i] = new AnimationClipPair
        {
            originalClip  = _overrideController[originalNames[i]],
            overrideClip  = clips[i]
        };
    }

    _overrideController.ApplyOverrides(
        new System.Collections.Generic.List<System.Collections.Generic.KeyValuePair<AnimationClip, AnimationClip>>(
            System.Array.ConvertAll(overrides, p =>
                new System.Collections.Generic.KeyValuePair<AnimationClip, AnimationClip>(
                    p.originalClip, p.overrideClip))
        )
    );
}
```
