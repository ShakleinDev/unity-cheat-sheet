# Unity Паттерн Фабрика — Создание объектов

```csharp
// Интерфейс для врага
public interface IEnemy {
    void Attack();
    void TakeDamage(int damage);
}

// Конкретная реализация врага: Гоблин
public class Goblin : IEnemy {
    public void Attack() => Debug.Log("Гоблин атакует!");
    public void TakeDamage(int damage) => Debug.Log($"Гоблин получает {damage} урона.");
}

// Конкретная реализация врага: Орк
public class Orc : IEnemy {
    public void Attack() => Debug.Log("Орк атакует!");
    public void TakeDamage(int damage) => Debug.Log($"Орк получает {damage} урона.");
}

// Интерфейс фабрики для создания врагов
public interface IEnemyFactory {
    IEnemy CreateEnemy();
}

// Конкретная реализация фабрики: GoblinFactory
public class GoblinFactory : IEnemyFactory {
    public IEnemy CreateEnemy() => new Goblin();
}

// Конкретная реализация фабрики: OrcFactory
public class OrcFactory : IEnemyFactory {
    public IEnemy CreateEnemy() => new Orc();
}

// Клиентский класс, использующий фабрику для создания врагов и взаимодействия с ними
public class GameManager : MonoBehaviour {
    private void Start() {
        InteractWithEnemy(new GoblinFactory());
        InteractWithEnemy(new OrcFactory());

        // Можно добавлять новые конкретные реализации IEnemy
        // без изменения существующего клиентского кода,
        // следуя принципу открытости/закрытости из SOLID
    }

    private void InteractWithEnemy(IEnemyFactory factory) {
        IEnemy enemy = factory.CreateEnemy();

        // Единообразное взаимодействие независимо от типа врага
        enemy.Attack();
        enemy.TakeDamage(20);
    }
}
```

