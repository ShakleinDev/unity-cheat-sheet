# Инстанцирование

## Базовый вызов Instantiate

```csharp
[SerializeField] GameObject _prefab;

// Создать копию префаба в позиции (0,0,0) без поворота
GameObject instance = Instantiate(_prefab);
```

## Позиция, поворот и родитель

```csharp
// С позицией и поворотом
GameObject obj = Instantiate(_prefab, new Vector3(1f, 0f, 3f), Quaternion.identity);

// С поворотом как у другого объекта
GameObject obj2 = Instantiate(_prefab, spawnPoint.position, spawnPoint.rotation);

// Как дочерний объект (сохраняет мировые координаты)
GameObject child = Instantiate(_prefab, parent.position, Quaternion.identity, parent);

// Как дочерний объект с локальными координатами (0,0,0) относительно родителя
GameObject child2 = Instantiate(_prefab, parent);
child2.transform.localPosition = Vector3.zero;
```

## Получение компонента после инстанцирования

```csharp
// Способ 1 — GetComponent после Instantiate
GameObject obj = Instantiate(_enemyPrefab, spawnPos, Quaternion.identity);
Enemy enemy = obj.GetComponent<Enemy>();
enemy.Initialize(difficulty: 2);

// Способ 2 — Instantiate с типом (не нужен GetComponent)
Enemy enemy2 = Instantiate(_enemyPrefab, spawnPos, Quaternion.identity)
    .GetComponent<Enemy>();

// Способ 3 — если префаб — это сам скрипт (рекомендуется)
[SerializeField] Enemy _enemyPrefab; // ссылка на компонент, а не GameObject

Enemy enemy3 = Instantiate(_enemyPrefab, spawnPos, Quaternion.identity);
enemy3.Initialize(difficulty: 2);
```

> ✅ Рекомендуется хранить ссылку на конкретный компонент (`Enemy _prefab`), а не на `GameObject` — это избавляет от лишнего `GetComponent` и даёт проверку типа на этапе компиляции.

## Уничтожение объектов

```csharp
// Немедленное уничтожение
Destroy(gameObject);

// Уничтожение другого объекта
Destroy(enemyObject);

// Уничтожение с задержкой (в секундах)
Destroy(gameObject, 3f);

// Уничтожение только компонента (объект остаётся)
Destroy(GetComponent<Rigidbody>());

// DestroyImmediate — только для Editor-скриптов, не использовать в рантайме
#if UNITY_EDITOR
DestroyImmediate(obj);
#endif
```

```csharp
// ⚠️ Частая ошибка — обращение к объекту после Destroy
Destroy(enemy.gameObject);
enemy.TakeDamage(10); // NullReferenceException!

// ✅ Правильно — сначала выполнить действия, потом уничтожить
enemy.Die();          // логика смерти
Destroy(enemy.gameObject, 0.5f); // уничтожить после анимации

// Проверка на уничтоженный объект
if (enemy != null)    // работает, Unity перегружает оператор ==
    enemy.TakeDamage(10);
```
