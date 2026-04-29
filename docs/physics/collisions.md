# Unity Коллизии и Триггеры - OnCollisionEnter и OnTriggerEnter

Unity предоставляет два типа определения столкновений: **Коллизии** (на основе физики) и **Триггеры** (определение перекрытия).

## Коллизия vs Триггер: Когда что использовать

| Случай использования | Тип | Пример |
|----------------------|-----|--------|
| Физические взаимодействия | **Коллизия** | Игрок ударяется о стену, мяч отскакивает |
| Определение перекрытия без физики | **Триггер** | Подбор монет, вход в зону |
| Урон при столкновении | **Коллизия** | Пуля попадает во врага с отдачей |
| Урон без столкновения | **Триггер** | Ходьба по лаве, лазерный луч |

## Требования

Чтобы события коллизии/триггера срабатывали, необходимо:

| Требование | События коллизии | События триггера |
|------------|------------------|------------------|
| Коллайдер на обоих объектах | Да | Да |
| Rigidbody хотя бы на одном | Да | Да |
| Включён "Is Trigger" | Нет | Да (хотя бы на одном) |

---

## 3D События Коллизии

Вызываются при физическом столкновении коллайдеров. Объекты отскакивают/взаимодействуют на основе физики.

### OnCollisionEnter

Вызывается один раз при начале столкновения.

```csharp
void OnCollisionEnter(Collision collision)
{
    Debug.Log("Удар: " + collision.gameObject.name);
}
```

### OnCollisionStay

Вызывается каждый кадр, пока столкновение продолжается.

```csharp
void OnCollisionStay(Collision collision)
{
    Debug.Log("Всё ещё касается: " + collision.gameObject.name);
}
```

### OnCollisionExit

Вызывается один раз при завершении столкновения.

```csharp
void OnCollisionExit(Collision collision)
{
    Debug.Log("Прекратил касаться: " + collision.gameObject.name);
}
```

### Свойства класса Collision

Параметр `Collision` предоставляет подробную информацию о столкновении:

```csharp
void OnCollisionEnter(Collision collision)
{
    // GameObject, с которым столкнулись
    GameObject other = collision.gameObject;
    
    // Rigidbody, с которым столкнулись (может быть null)
    Rigidbody otherRb = collision.rigidbody;
    
    // Коллайдер, с которым столкнулись
    Collider otherCollider = collision.collider;
    
    // Относительная скорость столкновения
    Vector3 impactVelocity = collision.relativeVelocity;
    float impactForce = impactVelocity.magnitude;
    
    // Суммарный импульс, применённый при столкновении
    Vector3 impulse = collision.impulse;
    
    // Количество точек контакта
    int contactCount = collision.contactCount;
    
    // Первая точка контакта
    if (contactCount > 0)
    {
        ContactPoint contact = collision.GetContact(0);
        Vector3 hitPoint = contact.point;
        Vector3 hitNormal = contact.normal;
    }
}
```

### Практический пример: Урон при столкновении

```csharp
public class DamageOnCollision : MonoBehaviour
{
    public float damageMultiplier = 10f;
    public float minImpactForce = 5f;

    void OnCollisionEnter(Collision collision)
    {
        float impactForce = collision.relativeVelocity.magnitude;
        
        if (impactForce > minImpactForce)
        {
            float damage = impactForce * damageMultiplier;
            
            // Попытка получить компонент Health у объекта столкновения
            Health health = collision.gameObject.GetComponent<Health>();
            if (health != null)
            {
                health.TakeDamage(damage);
            }
        }
    }
}
```

---

## 3D События Триггера

Вызываются при перекрытии коллайдеров. Физическая реакция не происходит — объекты проходят сквозь друг друга.

> **Настройка:** Включите "Is Trigger" хотя бы на одном из коллайдеров в Инспекторе.

### OnTriggerEnter

Вызывается один раз, когда другой коллайдер входит в триггер.

```csharp
void OnTriggerEnter(Collider other)
{
    Debug.Log(other.gameObject.name + " вошёл в триггер");
}
```

### OnTriggerStay

Вызывается каждый кадр, пока другой коллайдер находится внутри триггера.

```csharp
void OnTriggerStay(Collider other)
{
    Debug.Log(other.gameObject.name + " находится внутри триггера");
}
```

### OnTriggerExit

Вызывается один раз, когда другой коллайдер покидает триггер.

```csharp
void OnTriggerExit(Collider other)
{
    Debug.Log(other.gameObject.name + " вышел из триггера");
}
```

### Практический пример: Подбираемый предмет

```csharp
public class PickupItem : MonoBehaviour
{
    public int coinValue = 10;
    public AudioClip pickupSound;

    void OnTriggerEnter(Collider other)
    {
        // Реагировать только на игрока
        if (other.CompareTag("Player"))
        {
            // Добавить монеты игроку
            PlayerScore score = other.GetComponent<PlayerScore>();
            if (score != null)
            {
                score.AddCoins(coinValue);
            }
            
            // Воспроизвести звук в точке подбора
            if (pickupSound != null)
            {
                AudioSource.PlayClipAtPoint(pickupSound, transform.position);
            }
            
            // Уничтожить предмет
            Destroy(gameObject);
        }
    }
}
```

### Практический пример: Зона урона

```csharp
public class DamageZone : MonoBehaviour
{
    public float damagePerSecond = 10f;

    void OnTriggerStay(Collider other)
    {
        Health health = other.GetComponent<Health>();
        if (health != null)
        {
            health.TakeDamage(damagePerSecond * Time.deltaTime);
        }
    }
}
```

---

## 2D События Коллизии

Для 2D игр используйте 2D-варианты с типами `Collision2D` и `Collider2D`.

### События коллизии (2D)

```csharp
void OnCollisionEnter2D(Collision2D collision)
{
    Debug.Log("Удар: " + collision.gameObject.name);
    
    // Свойства, специфичные для 2D
    Vector2 contactPoint = collision.GetContact(0).point;
    Vector2 contactNormal = collision.GetContact(0).normal;
}

void OnCollisionStay2D(Collision2D collision)
{
    Debug.Log("Всё ещё касается: " + collision.gameObject.name);
}

void OnCollisionExit2D(Collision2D collision)
{
    Debug.Log("Прекратил касаться: " + collision.gameObject.name);
}
```

### События триггера (2D)

```csharp
void OnTriggerEnter2D(Collider2D other)
{
    Debug.Log(other.gameObject.name + " вошёл в триггер");
}

void OnTriggerStay2D(Collider2D other)
{
    Debug.Log(other.gameObject.name + " находится внутри триггера");
}

void OnTriggerExit2D(Collider2D other)
{
    Debug.Log(other.gameObject.name + " вышел из триггера");
}
```

### Пример 2D-платформера: Проверка земли

```csharp
public class GroundCheck : MonoBehaviour
{
    public bool isGrounded = false;

    void OnTriggerEnter2D(Collider2D other)
    {
        if (other.CompareTag("Ground"))
        {
            isGrounded = true;
        }
    }

    void OnTriggerExit2D(Collider2D other)
    {
        if (other.CompareTag("Ground"))
        {
            isGrounded = false;
        }
    }
}
```

---

## Общие паттерны

### Проверка тегов

Фильтрация коллизий по тегу с помощью `CompareTag()` (быстрее, чем `==`):

```csharp
void OnCollisionEnter(Collision collision)
{
    if (collision.gameObject.CompareTag("Enemy"))
    {
        // Обработка столкновения с врагом
    }
    else if (collision.gameObject.CompareTag("Pickup"))
    {
        // Обработка столкновения с предметом
    }
}
```

### Проверка слоёв

Фильтрация по слою для более сложных сценариев:

```csharp
void OnTriggerEnter(Collider other)
{
    // Проверить, находится ли объект на слое "Interactable"
    if (other.gameObject.layer == LayerMask.NameToLayer("Interactable"))
    {
        // Обработка взаимодействия
    }
}
```

### Получение компонентов

Доступ к компонентам на столкнувшемся объекте:

```csharp
void OnCollisionEnter(Collision collision)
{
    // Попытка получить компонент (возвращает null, если не найден)
    Enemy enemy = collision.gameObject.GetComponent<Enemy>();
    if (enemy != null)
    {
        enemy.TakeDamage(10);
    }
    
    // TryGetComponent немного эффективнее (Unity 2019.2+)
    if (collision.gameObject.TryGetComponent<Enemy>(out Enemy e))
    {
        e.TakeDamage(10);
    }
}
```

### Уничтожение при столкновении

```csharp
void OnCollisionEnter(Collision collision)
{
    if (collision.gameObject.CompareTag("Enemy"))
    {
        // Уничтожить врага
        Destroy(collision.gameObject);
        
        // Уничтожить этот объект (например, пулю)
        Destroy(gameObject);
    }
}
```

---

## Матрица Коллизий

Не все комбинации коллайдеров генерируют события. Упрощённая справка:

### События коллизии (OnCollisionEnter и др.)

| Объект А | Объект Б | События срабатывают? |
|----------|----------|----------------------|
| Rigidbody | Статический коллайдер | Да |
| Rigidbody | Rigidbody | Да |
| Rigidbody | Кинематический Rigidbody | Да |
| Кинематический | Статический коллайдер | Нет |
| Кинематический | Кинематический | Нет |
| Статический | Статический | Нет |

### События триггера (OnTriggerEnter и др.)

| Объект А | Объект Б | События срабатывают? |
|----------|----------|----------------------|
| Rigidbody-триггер | Любой коллайдер | Да |
| Кинематический триггер | Любой коллайдер | Да |
| Статический триггер | Rigidbody/Кинематический | Да |
| Статический триггер | Статический коллайдер | Нет |

### Справка по типам коллайдеров

| Тип | Rigidbody | Is Kinematic | Is Trigger |
|-----|-----------|--------------|------------|
| Статический коллайдер | Нет | - | Выкл |
| Коллайдер с Rigidbody | Да | Выкл | Выкл |
| Кинематический коллайдер | Да | Вкл | Выкл |
| Статический триггер | Нет | - | Вкл |
| Rigidbody-триггер | Да | Выкл | Вкл |
| Кинематический триггер | Да | Вкл | Вкл |

---

## Распространённые ошибки

### 1. Отсутствует Rigidbody

**Проблема:** События коллизии не срабатывают.

**Решение:** Хотя бы один объект должен иметь Rigidbody (или Rigidbody2D для 2D).

```csharp
// Этот скрипт не будет получать события коллизии, если ни этот объект,
// ни столкнувшийся объект не имеют Rigidbody
void OnCollisionEnter(Collision collision) { } // Никогда не вызывается!
```

### 2. Оба объекта кинематические

**Проблема:** Два кинематических Rigidbody не генерируют события коллизии.

**Решение:** Сделайте хотя бы один некинематическим, или используйте триггеры.

### 3. Триггер не включён

**Проблема:** `OnTriggerEnter` не вызывается.

**Решение:** Включите "Is Trigger" хотя бы на одном коллайдере в Инспекторе.

### 4. Перепутаны методы 2D/3D

**Проблема:** Использование `OnCollisionEnter` в 2D игре.

**Решение:** Используйте `OnCollisionEnter2D` с 2D коллайдерами.

```csharp
// НЕВЕРНО для 2D игр
void OnCollisionEnter(Collision collision) { }

// ВЕРНО для 2D игр  
void OnCollisionEnter2D(Collision2D collision) { }
```

### 5. Матрица коллизий слоёв

**Проблема:** Объекты на определённых слоях не сталкиваются.

**Решение:** Проверьте **Edit > Project Settings > Physics > Layer Collision Matrix**.

### 6. Масштаб влияет на коллайдеры

**Проблема:** Коллайдеры не совпадают с визуальным мешем после масштабирования.

**Решение:** Примените масштаб в программе моделирования или вручную скорректируйте размер коллайдера.

### 7. Неверная сигнатура метода

**Проблема:** Методы коллизии не вызываются.

**Решение:** Убедитесь в точной сигнатуре метода. Частые ошибки:

```csharp
// НЕВЕРНО - строчная 'c'
void onCollisionEnter(Collision collision) { }

// НЕВЕРНО - отсутствует параметр
void OnCollisionEnter() { }

// НЕВЕРНО - неверный тип параметра
void OnCollisionEnter(Collider other) { } // Должен быть Collision

// ВЕРНО
void OnCollisionEnter(Collision collision) { }
```

---

## Краткая Справка

### 3D Методы

| Метод | Параметр | Когда вызывается |
|-------|----------|------------------|
| `OnCollisionEnter(Collision)` | `Collision` | Начало столкновения |
| `OnCollisionStay(Collision)` | `Collision` | Каждый кадр во время столкновения |
| `OnCollisionExit(Collision)` | `Collision` | Конец столкновения |
| `OnTriggerEnter(Collider)` | `Collider` | Вход в триггер |
| `OnTriggerStay(Collider)` | `Collider` | Каждый кадр внутри триггера |
| `OnTriggerExit(Collider)` | `Collider` | Выход из триггера |

### 2D Методы

| Метод | Параметр | Когда вызывается |
|-------|----------|------------------|
| `OnCollisionEnter2D(Collision2D)` | `Collision2D` | Начало столкновения |
| `OnCollisionStay2D(Collision2D)` | `Collision2D` | Каждый кадр во время столкновения |
| `OnCollisionExit2D(Collision2D)` | `Collision2D` | Конец столкновения |
| `OnTriggerEnter2D(Collider2D)` | `Collider2D` | Вход в триггер |
| `OnTriggerStay2D(Collider2D)` | `Collider2D` | Каждый кадр внутри триггера |
| `OnTriggerExit2D(Collider2D)` | `Collider2D` | Выход из триггера |

---

## См. также

- [Rigidbody](rigidbody.md) - Настройка физического тела
- [Raycast](raycast.md) - Определение столкновений на основе лучей
- [Ignore Collision](ignore-collision.md) - Отключение определённых столкновений
