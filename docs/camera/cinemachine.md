# Cinemachine

**Cinemachine** — пакет Unity для профессионального управления камерой. Позволяет настраивать следование, переходы, тряску и сложное поведение камеры без написания кода.

**Установка:** *Window → Package Manager → Cinemachine → Install*

```csharp
using Unity.Cinemachine;
```

## Virtual Camera

`CinemachineCamera` (до v3: `CinemachineVirtualCamera`) — виртуальная камера, которая описывает *желаемое* поведение. Реальная камера с компонентом `CinemachineBrain` следует за активной виртуальной камерой.

**Создание:** *GameObject → Cinemachine → Cinemachine Camera*

Основные настройки в инспекторе:
- **Follow** — Transform цели, за которой движется камера
- **Look At** — Transform цели, на которую смотрит камера
- **Priority** — при нескольких вирт. камерах активна та, у которой Priority выше

```csharp
// Смена цели в рантайме
CinemachineCamera vcam = GetComponent<CinemachineCamera>();
vcam.Follow = newTarget;
vcam.LookAt  = newTarget;

// Получить активную виртуальную камеру
ICinemachineCamera active = CinemachineCore.GetActiveBrain(0).ActiveVirtualCamera;
```

## Body и Aim

**Body** — определяет, как камера перемещается за целью.

| Компонент | Поведение |
|-----------|-----------|
| `CinemachineFollow` | Следует с заданным смещением (Offset) |
| `CinemachineOrbitalFollow` | Орбитальное вращение вокруг цели |
| `CinemachineHardLockToTarget` | Позиция строго совпадает с целью |
| `CinemachinePositionComposer` | Удерживает цель в заданной области экрана |

**Aim** — определяет, как камера поворачивается.

| Компонент | Поведение |
|-----------|-----------|
| `CinemachineRotationComposer` | Удерживает цель в заданной зоне экрана с демпфированием |
| `CinemachineHardLookAt` | Всегда смотрит строго на цель |
| `CinemachinePanTilt` | Управление углами Pan/Tilt (например, от мыши) |

```csharp
// Изменить смещение Follow в рантайме
var follow = vcam.GetComponent<CinemachineFollow>();
follow.FollowOffset = new Vector3(0f, 3f, -8f);
```

## Переходы между камерами

`CinemachineBrain` на основной камере автоматически управляет переходами.

```csharp
// Переключить камеру через Priority
[SerializeField] CinemachineCamera _mainCam;
[SerializeField] CinemachineCamera _aimCam;

void StartAiming()
{
    _aimCam.Priority  = 20;
    _mainCam.Priority = 10;
}

void StopAiming()
{
    _aimCam.Priority  = 10;
    _mainCam.Priority = 20;
}
```

Настройка перехода в Brain (**Default Blend**):
- **Cut** — мгновенный переход
- **Ease In Out** — плавное ускорение/замедление
- **Linear** — линейное смешивание
- **Custom Curve** — кастомная кривая

```csharp
// Настройка через код
var brain = Camera.main.GetComponent<CinemachineBrain>();
brain.DefaultBlend = new CinemachineBlendDefinition(
    CinemachineBlendDefinition.Styles.EaseInOut, 0.5f
);
```

## CinemachineStateDrivenCamera

Автоматически переключает виртуальные камеры в зависимости от состояния Animator.

**Настройка:**
1. *GameObject → Cinemachine → State-Driven Camera*
2. Назначить **Animated Target** — объект с Animator
3. В таблице States привязать состояния Animator к нужным виртуальным камерам

```csharp
// Смена камеры происходит автоматически при смене состояния аниматора
// Код управляет только аниматором:
_animator.SetBool("IsAiming", true); // → камера переключится на AimCamera
```

## Impulse Source — тряска камеры

Cinemachine Impulse — встроенная система тряски через физические импульсы.

**Настройка:**
1. На камеру добавить `CinemachineImpulseListener`
2. На объект-источник тряски добавить `CinemachineImpulseSource`

```csharp
using Unity.Cinemachine;

public class ExplosionShake : MonoBehaviour
{
    [SerializeField] CinemachineImpulseSource _impulseSource;

    public void Explode()
    {
        // Генерировать импульс с заданной силой
        _impulseSource.GenerateImpulse(force: 1.5f);
    }

    public void ExplodeDirectional()
    {
        // Импульс в заданном направлении
        _impulseSource.GenerateImpulseWithVelocity(Vector3.up * 2f);
    }
}
```
