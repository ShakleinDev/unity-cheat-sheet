# Unity — Паттерн «Стратегия» (Взаимозаменяемые алгоритмы)

Паттерн «Стратегия» определяет семейство алгоритмов, инкапсулирует каждый из них и делает их взаимозаменяемыми. Паттерн позволяет изменять алгоритм независимо от клиентов, которые его используют.

#### Базовый пример
```csharp
// Интерфейс стратегии
public interface IStrategy {
    void Execute();
}

// Пример реализации стратегии
public class AttackStrategy : IStrategy {
    public void Execute() => Debug.Log("Выполняется атака!");
}

// Контекстный класс, использующий стратегию
public class Character : MonoBehaviour {
    private IStrategy strategy;

    public void SetStrategy(IStrategy newStrategy) {
        strategy = newStrategy;
    }

    public void ExecuteStrategy() {
        strategy?.Execute();
    }
}
```

#### Подробный пример — Боевая система
Полный пример реализации гибкой боевой системы с использованием паттерна «Стратегия». Реализация демонстрирует:
- Определение различных стратегий атаки (ближний бой, дальний бой, область)
- Переключение между стратегиями во время выполнения
- Инкапсуляцию боевых алгоритмов

👉 [Посмотреть полную реализацию](https://github.com/ozankasikci/unity-cheat-sheet/tree/master/Patterns/StrategyPattern/README.md)

