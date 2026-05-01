# Вложенные префабы

**Вложенный префаб (Nested Prefab)** — это префаб, размещённый внутри другого префаба. Каждый из них остаётся независимым ассетом и может редактироваться отдельно.

## Создание вложенного префаба

1. Открыть родительский префаб в Prefab Mode (двойной клик)
2. Перетащить другой префаб-ассет из Project в иерархию родителя
3. Сохранить изменения — теперь дочерний объект является вложенным префабом

```
Prefabs/
  Character.prefab          ← родительский
    ├─ Body
    ├─ Hand_R
    │    └─ Weapon.prefab   ← вложенный префаб
    └─ Hand_L
```

## Override на разных уровнях

Изменения вложенного префаба можно применить на двух уровнях:

| Действие | Результат |
|----------|-----------|
| **Apply to Prefab `Weapon`** | Изменение применяется к ассету `Weapon.prefab` — затронет все его инстансы везде |
| **Apply to Prefab `Character`** | Изменение сохраняется только внутри `Character.prefab` как Override |
| **Revert** | Откат к значению ассета `Weapon.prefab` |

```csharp
#if UNITY_EDITOR
// Получить ближайший корневой ассет префаба для инстанса
string assetPath = PrefabUtility.GetPrefabAssetPathOfNearestInstanceRoot(instance);

// Проверить, является ли объект частью вложенного префаба
bool isNested = PrefabUtility.IsPartOfNonAssetPrefabInstance(instance)
             && PrefabUtility.GetCorrespondingObjectFromSource(instance) != null;
#endif
```

## Практический пример

**Структура: персонаж с заменяемым оружием**

```
Character.prefab
  ├─ Mesh (SkinnedMeshRenderer)
  ├─ Animator
  ├─ Hand_R (Transform — точка крепления)
  │    └─ Sword.prefab       ← заменяемый вложенный префаб
  └─ CharacterController
```

```csharp
// Замена вложенного префаба оружия в рантайме
public class WeaponSlot : MonoBehaviour
{
    [SerializeField] Transform _attachPoint; // Hand_R
    GameObject _currentWeapon;

    public void EquipWeapon(GameObject weaponPrefab)
    {
        // Убрать текущее оружие
        if (_currentWeapon != null)
            Destroy(_currentWeapon);

        // Создать новое в точке крепления
        _currentWeapon = Instantiate(weaponPrefab, _attachPoint);
        _currentWeapon.transform.localPosition = Vector3.zero;
        _currentWeapon.transform.localRotation = Quaternion.identity;
    }
}
```

> ✅ Преимущество вложенных префабов перед одним большим префабом: каждая часть (оружие, шлем, броня) редактируется независимо и переиспользуется в разных персонажах.
