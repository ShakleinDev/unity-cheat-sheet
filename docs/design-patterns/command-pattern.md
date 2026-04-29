# Паттерн Команда в Unity — Система Отмены/Повтора действий

```csharp
// Интерфейс команды
public interface ICommand {
    void Execute();
}

// Конкретные классы команд
public class MoveCommand : ICommand {
    private Transform transform;
    private Vector3 direction;
    private float distance;

    public MoveCommand(Transform transform, Vector3 direction, float distance) {
        this.transform = transform;
        this.direction = direction;
        this.distance = distance;
    }

    public void Execute() {
        transform.Translate(direction * distance);
    }
}

// Класс инициатора (Invoker)
public class CommandInvoker {
    private Stack<ICommand> commandStack = new Stack<ICommand>();

    public void ExecuteCommand(ICommand command) {
        commandStack.Push(command);
        command.Execute();
    }

    public void Undo() {
        if (commandStack.Count > 0) {
            var command = commandStack.Pop();
            // При необходимости реализуйте метод отмены
        }
    }
}

// Использование
public class CommandUser : MonoBehaviour {
    private CommandInvoker invoker = new CommandInvoker();

    void Update() {
        if (Input.GetKeyDown(KeyCode.UpArrow)) {
            ICommand moveUp = new MoveCommand(transform, Vector3.up, 1.0f);
            invoker.ExecuteCommand(moveUp);
        }

        // Добавьте другие направления и invoker.Undo() для отмены действий
    }
}
```

