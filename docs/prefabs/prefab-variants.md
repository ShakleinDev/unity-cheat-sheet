# Варианты префабов (Prefab Variants)

**Prefab Variant** — это префаб, наследующий все свойства базового префаба, но способный переопределять любые из них. Аналог наследования классов, но для GameObject.

## Создание варианта

**Способ 1 — из ассета:**
1. ПКМ на префабе в Project → **Create → Prefab Variant**
2. Появится новый `.prefab` со значком варианта (стрелка в углу иконки)

**Способ 2 — из инстанса в сцене:**
1. Изменить инстанс префаба в сцене
2. В инспекторе: **Overrides → Apply As New Variant...**
3. Выбрать место сохранения

## Переопределение свойств

В варианте можно переопределить любое свойство базового префаба — остальные будут унаследованы.

```
Enemy.prefab (базовый)
  ├─ Health: 100
  ├─ Speed: 3.0
  ├─ MeshRenderer: enemy_base_mesh
  └─ Color: серый

EnemyFast.prefab (вариант)
  ├─ Health: 100      ← унаследовано
  ├─ Speed: 7.0       ← переопределено (Override)
  ├─ MeshRenderer: enemy_base_mesh ← унаследовано
  └─ Color: жёлтый    ← переопределено (Override)
```

Переопределённые свойства отображаются **жирным** в инспекторе, как обычные Override.

## Наследование изменений базового префаба

Изменения базового префаба **автоматически** применяются ко всем вариантам — если только вариант не переопределил соответствующее свойство.

```
Изменили Enemy.prefab: Health: 100 → 150
  ├─ EnemyFast.prefab:  Health → 150 ✅ (унаследовал)
  ├─ EnemyBig.prefab:   Health → 150 ✅ (унаследовал)
  └─ EnemyBoss.prefab:  Health → 500 ✅ (Override — не изменился)
```

## Практический пример

**Семейство врагов на основе одного базового префаба:**

```
Enemy.prefab (базовый)
  ├─ EnemyAI (скрипт)
  ├─ Rigidbody
  ├─ CapsuleCollider
  └─ Stats: hp=100, speed=3, damage=10

Варианты:
  EnemyFast.prefab   — speed=7,  hp=60,  damage=8,  scale=(0.8, 0.8, 0.8)
  EnemyBig.prefab    — speed=1.5, hp=300, damage=25, scale=(1.5, 1.5, 1.5)
  EnemyRanged.prefab — speed=2,  hp=80,  damage=15, + добавлен компонент RangedAttack
  EnemyBoss.prefab   — speed=2,  hp=1000, damage=40, другой Mesh и материал
```

```csharp
// Спаунер, работающий с любым вариантом через базовый тип
public class EnemySpawner : MonoBehaviour
{
    [SerializeField] Enemy[] _enemyVariants; // базовый тип для всех вариантов

    public Enemy SpawnRandom(Vector3 position)
    {
        Enemy prefab = _enemyVariants[Random.Range(0, _enemyVariants.Length)];
        return Instantiate(prefab, position, Quaternion.identity);
    }
}
```

> ✅ Prefab Variants значительно сокращают дублирование: при обновлении базового префаба (новая анимация, исправление коллайдера) все варианты получат изменение автоматически.
