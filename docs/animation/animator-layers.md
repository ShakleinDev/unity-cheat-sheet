# Слои и маски аватара

Слои Animator позволяют воспроизводить разные анимации на разных частях тела одновременно. Например, ноги выполняют анимацию бега, а руки — анимацию стрельбы.

## Avatar Mask

Avatar Mask определяет, какие кости тела затрагивает данный слой.

**Создание:**
1. *Assets → Create → Avatar Mask*
2. В инспекторе включить/выключить нужные части тела (или отдельные кости)
3. Назначить маску слою: в Animator Controller → выбрать слой → настройки шестерёнки → поле **Mask**

```
Body parts (гуманоид):
  ✅ Head
  ✅ Left Arm
  ✅ Right Arm
  ❌ Left Leg
  ❌ Right Leg
  ❌ Root
```

Маска также поддерживает режим **Transform** — там можно выбрать конкретные кости по имени (для не-гуманоидных моделей).

## Настройка веса слоя

Вес (Weight) определяет, насколько сильно слой влияет на итоговую позу. Значение от `0` (нет влияния) до `1` (полное влияние).

```csharp
Animator _animator;

void Awake() => _animator = GetComponent<Animator>();

// Плавное включение верхнего слоя при прицеливании
void SetAimLayer(bool aiming)
{
    float targetWeight = aiming ? 1f : 0f;
    StartCoroutine(FadeLayerWeight(1, targetWeight, 0.2f));
}

IEnumerator FadeLayerWeight(int layerIndex, float target, float duration)
{
    float start   = _animator.GetLayerWeight(layerIndex);
    float elapsed = 0f;

    while (elapsed < duration)
    {
        elapsed += Time.deltaTime;
        float weight = Mathf.Lerp(start, target, elapsed / duration);
        _animator.SetLayerWeight(layerIndex, weight);
        yield return null;
    }

    _animator.SetLayerWeight(layerIndex, target);
}
```

## Additive vs Override

| Режим | Описание |
|-------|----------|
| **Override** | Слой полностью заменяет позу нижних слоёв в пределах маски |
| **Additive** | Слой прибавляет дельту анимации поверх нижних слоёв |

**Override** — стандартный режим. Подходит для замены анимации рук при смене оружия.

**Additive** — подходит для наложения тонких эффектов: покачивание от усталости, дыхание, лёгкий наклон при повороте.

```csharp
// Пример: анимация дыхания на верхнем слое в режиме Additive
// Слой 0 (Base): бег/ходьба
// Слой 1 (Breathing, Additive, Weight=1): лёгкое движение грудной клетки

// В коде ничего особенного — просто управляем весом при необходимости
_animator.SetLayerWeight(1, 1f); // всегда активен
```

## Пример: бег + стрельба одновременно

```
Слой 0 — Base Layer (Override, Weight=1)
  └─ Blend Tree: Idle / Walk / Run  (маска: всё тело)

Слой 1 — UpperBody Layer (Override, Weight=1)
  └─ Состояния: Idle_Upper / Shoot / Reload  (маска: только верхняя часть тела)
```

```csharp
void Update()
{
    // Нижний слой управляется скоростью
    _animator.SetFloat("Speed", _rigidbody.linearVelocity.magnitude);

    // Верхний слой управляется отдельно
    if (Input.GetButtonDown("Fire1"))
        _animator.SetTrigger("Shoot");

    if (Input.GetKeyDown(KeyCode.R))
        _animator.SetTrigger("Reload");
}
```
