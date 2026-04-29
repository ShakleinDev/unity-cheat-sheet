# Unity Игнорирование Коллизий - Отключение Физических Взаимодействий

Отключение обнаружения столкновений между конкретными коллайдерами или целыми слоями.

> Для обработки событий столкновений и триггеров см. [Коллизии и Триггеры](collisions.md)

## Physics.IgnoreCollision

Игнорирование столкновений между двумя конкретными коллайдерами:

```csharp
// Заставляет систему обнаружения коллизий игнорировать все столкновения между collider1 и collider2.
public static void IgnoreCollision(Collider collider1, Collider collider2, bool ignore = true);

// Пример: Отключить столкновение между союзником и пулей
Transform bullet;
Transform ally;
Physics.IgnoreCollision(bullet.GetComponent<Collider>(), ally.GetComponent<Collider>());
```

### Практический Пример: Созданный Снаряд

```csharp
public class ProjectileSpawner : MonoBehaviour
{
    public GameObject projectilePrefab;

    void Fire()
    {
        GameObject projectile = Instantiate(projectilePrefab, transform.position, transform.rotation);
        
        // Предотвратить столкновение снаряда с объектом, который его создал
        Physics.IgnoreCollision(projectile.GetComponent<Collider>(), GetComponent<Collider>());
    }
}
```

## Physics.IgnoreLayerCollision

Игнорирование столкновений между целыми слоями (более эффективно при большом количестве объектов):

```csharp
// Игнорировать столкновения между слоем 8 (например, "Player") и слоем 9 (например, "PlayerProjectiles")
Physics.IgnoreLayerCollision(8, 9, true);

// Использование LayerMask для удобочитаемости
int playerLayer = LayerMask.NameToLayer("Player");
int projectileLayer = LayerMask.NameToLayer("PlayerProjectiles");
Physics.IgnoreLayerCollision(playerLayer, projectileLayer, true);
```

### Восстановление Коллизий

```csharp
// Повторно включить столкновение между двумя коллайдерами
Physics.IgnoreCollision(collider1, collider2, false);

// Повторно включить столкновение между слоями
Physics.IgnoreLayerCollision(playerLayer, projectileLayer, false);
```

## Матрица Коллизий Слоёв (Редактор)

Для постоянной настройки столкновений между слоями используйте:  
**Edit > Project Settings > Physics > Layer Collision Matrix**

Это эффективнее, чем вызовы `IgnoreLayerCollision` во время выполнения для статических конфигураций.

## Эквиваленты для 2D

```csharp
// Игнорировать столкновение между двумя 2D коллайдерами
Physics2D.IgnoreCollision(collider2D_1, collider2D_2, true);

// Игнорировать столкновение между 2D слоями
Physics2D.IgnoreLayerCollision(layer1, layer2, true);
```

## Смотрите Также

- [Коллизии и Триггеры](collisions.md) - Обработка событий столкновений
- [Raycast](raycast.md) - Обнаружение столкновений с помощью лучей

