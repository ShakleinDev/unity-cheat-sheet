# Unity Get Bone Transform - Animator.GetBoneTransform

Получите доступ к конкретной кости гуманоида (например, голова, рука, позвоночник), чтобы крепить реквизит, прицеливаться оружием или считывать данные позы. Humanoid-риг Unity предоставляет доступ к костям через `Animator.GetBoneTransform` после инициализации аватара.

## Поведение

1. Закэшировать ссылку на `Animator` при старте.
2. Вызвать `GetBoneTransform` с нужным значением перечисления `HumanBodyBones`.
3. Проверить результат — возвращает `null`, если аватар не является гуманоидом или кость не замаплена.
4. Использовать трансформ кости для привязки объектов, выравнивания эффектов или считывания мировой позиции/вращения каждый кадр.

## Пример

```csharp
using UnityEngine;

public class AttachToBone : MonoBehaviour
{
    [SerializeField] private Animator animator;
    [SerializeField] private HumanBodyBones bone = HumanBodyBones.RightHand;
    [SerializeField] private Transform objectToAttach;

    private Transform boneTransform;

    private void Awake()
    {
        if (!animator)
        {
            animator = GetComponentInParent<Animator>();
        }

        boneTransform = animator ? animator.GetBoneTransform(bone) : null;

        if (!boneTransform)
        {
            Debug.LogWarning($"Кость {bone} не найдена на аватаре", this);
            enabled = false;
            return;
        }

        objectToAttach.SetParent(boneTransform, worldPositionStays: false);
    }

    private void LateUpdate()
    {
        // Опционально: убедиться, что прикреплённый объект остаётся выровненным, если нельзя напрямую сделать его дочерним
        if (!objectToAttach) return;
        objectToAttach.localPosition = Vector3.zero;
        objectToAttach.localRotation = Quaternion.identity;
    }
}
```

### Примечания

- Работает только с Humanoid-аватарами; для Generic-ригов открывайте трансформы вручную или через события анимации.
- Вызывайте `GetBoneTransform` после завершения инициализации аниматора (например, в `Awake`/`Start` или позже), чтобы избежать возврата null.
- Используйте `LateUpdate` при корректировке прикреплённых объектов, чтобы они следовали за полностью вычисленной позой анимации.
- Кэшируйте трансформы костей вместо вызова `GetBoneTransform` каждый кадр — это недорогая операция, но позволяет избежать лишних обращений.
