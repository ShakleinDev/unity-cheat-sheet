# Управление префабами в рантайме

## Хранение ссылок на префабы

**Способ 1 — SerializeField (рекомендуется)**

```csharp
public class EnemySpawner : MonoBehaviour
{
    // Ссылка на компонент — лучше, чем на GameObject
    [SerializeField] Enemy _enemyPrefab;

    // Список вариантов
    [SerializeField] Enemy[] _enemyVariants;

    // Словарь по типу
    [SerializeField] EnemyPrefabEntry[] _prefabMap;

    [System.Serializable]
    public struct EnemyPrefabEntry
    {
        public EnemyType Type;
        public Enemy     Prefab;
    }
}
```

**Способ 2 — ScriptableObject как каталог префабов**

```csharp
[CreateAssetMenu(menuName = "Game/PrefabCatalog")]
public class PrefabCatalog : ScriptableObject
{
    public Enemy   EnemyBasic;
    public Enemy   EnemyBoss;
    public Bullet  BulletPrefab;
    public GameObject ExplosionVFX;
}
```

```csharp
public class GameManager : MonoBehaviour
{
    [SerializeField] PrefabCatalog _catalog;

    void SpawnBoss(Vector3 pos) =>
        Instantiate(_catalog.EnemyBoss, pos, Quaternion.identity);
}
```

## Загрузка через Resources.Load

Позволяет загружать префабы из папки `Resources/` динамически по пути.

```csharp
// Структура папок:
// Assets/Resources/Enemies/EnemyBasic.prefab
// Assets/Resources/Enemies/EnemyBoss.prefab

// Загрузка по пути (без расширения)
GameObject prefab = Resources.Load<GameObject>("Enemies/EnemyBasic");
Instantiate(prefab, spawnPos, Quaternion.identity);

// Загрузка компонента сразу
Enemy enemyPrefab = Resources.Load<Enemy>("Enemies/EnemyBasic");

// Загрузка всех префабов из папки
GameObject[] allEnemies = Resources.LoadAll<GameObject>("Enemies");
foreach (var e in allEnemies)
    Debug.Log(e.name);
```

> ⚠️ `Resources.Load` — синхронная операция. Для больших ассетов используйте **Addressables**. Всё содержимое папки `Resources/` включается в сборку независимо от того, используется ли ассет.

## Динамический пул объектов из префаба

Частое создание и уничтожение объектов через `Instantiate`/`Destroy` дорого обходится по производительности. Пул создаёт объекты заранее и переиспользует их.

```csharp
public class PrefabPool<T> : MonoBehaviour where T : Component
{
    [SerializeField] T    _prefab;
    [SerializeField] int  _initialSize = 10;
    [SerializeField] Transform _container;

    readonly Queue<T> _pool = new();

    void Awake()
    {
        for (int i = 0; i < _initialSize; i++)
            _pool.Enqueue(CreateNew());
    }

    T CreateNew()
    {
        T obj = Instantiate(_prefab, _container);
        obj.gameObject.SetActive(false);
        return obj;
    }

    public T Get(Vector3 position, Quaternion rotation)
    {
        T obj = _pool.Count > 0 ? _pool.Dequeue() : CreateNew();
        obj.transform.SetPositionAndRotation(position, rotation);
        obj.gameObject.SetActive(true);
        return obj;
    }

    public void Return(T obj)
    {
        obj.gameObject.SetActive(false);
        _pool.Enqueue(obj);
    }
}
```

```csharp
// Использование пула для пуль
public class BulletPool : PrefabPool<Bullet> { }

public class Gun : MonoBehaviour
{
    [SerializeField] BulletPool _pool;
    [SerializeField] Transform  _muzzle;

    void Shoot()
    {
        Bullet bullet = _pool.Get(_muzzle.position, _muzzle.rotation);
        bullet.Launch(direction: _muzzle.forward, onExpire: () => _pool.Return(bullet));
    }
}
```

> ✅ Начиная с Unity 2021, в движке есть встроенный `UnityEngine.Pool.ObjectPool<T>` — готовая реализация пула без необходимости писать свою.

```csharp
using UnityEngine.Pool;

public class BulletSpawner : MonoBehaviour
{
    [SerializeField] Bullet _prefab;

    IObjectPool<Bullet> _pool;

    void Awake()
    {
        _pool = new ObjectPool<Bullet>(
            createFunc:      () => Instantiate(_prefab),
            actionOnGet:     b  => b.gameObject.SetActive(true),
            actionOnRelease: b  => b.gameObject.SetActive(false),
            actionOnDestroy: b  => Destroy(b.gameObject),
            defaultCapacity: 20,
            maxSize:         100
        );
    }

    public void Shoot(Vector3 pos, Quaternion rot)
    {
        Bullet b = _pool.Get();
        b.transform.SetPositionAndRotation(pos, rot);
        b.Launch(onExpire: () => _pool.Release(b));
    }
}
```
