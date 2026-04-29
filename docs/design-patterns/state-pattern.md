# Unity — Паттерн «Состояние» (Конечные автоматы)

Паттерн «Состояние» позволяет объекту изменять своё поведение при изменении внутреннего состояния. Он инкапсулирует поведение, специфичное для каждого состояния, и делает переходы между состояниями явными.

#### Базовый пример
```csharp
// Интерфейс состояния
public interface IState {
    void Enter();
    void Update();
    void Exit();
}

// Пример реализации состояния
public class IdleState : IState {
    public void Enter() => Debug.Log("Вошли в состояние покоя");
    public void Update() => Debug.Log("Обновление состояния покоя");
    public void Exit() => Debug.Log("Вышли из состояния покоя");
}

// Машина состояний, управляющая переходами между состояниями
public class StateMachine : MonoBehaviour {
    private IState currentState;

    public void ChangeState(IState newState) {
        currentState?.Exit();
        currentState = newState;
        currentState?.Enter();
    }

    private void Update() {
        currentState?.Update();
    }
}
```

#### Подробный пример — система обучения (онбординга) в игре
Полный пример реализации системы онбординга/обучения с использованием паттерна «Состояние». Реализация демонстрирует:
- Управление различными состояниями обучения (движение, бой, инвентарь)
- Обработку переходов между состояниями
- Отслеживание прогресса игрока в процессе обучения

👉 [Посмотреть полную реализацию](https://github.com/ozankasikci/unity-cheat-sheet/tree/master/Patterns/StatePattern/README.md)

