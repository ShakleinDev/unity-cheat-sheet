# Unity — Паттерн «Цепочка обязанностей» (Chain of Responsibility)

Паттерн «Цепочка обязанностей» создаёт цепочку объектов-обработчиков для запроса. Каждый обработчик содержит ссылку на следующий в цепочке и решает — обработать запрос самостоятельно или передать его дальше.

#### Базовый пример
```csharp
// Интерфейс обработчика
public interface IHandler {
    void SetNext(IHandler handler);
    void HandleRequest(string request);
}

// Базовый класс обработчика
public abstract class BaseHandler : IHandler {
    protected IHandler nextHandler;

    public void SetNext(IHandler handler) {
        nextHandler = handler;
    }

    public virtual void HandleRequest(string request) {
        if (nextHandler != null) {
            nextHandler.HandleRequest(request);
        }
    }
}

// Конкретные обработчики
public class UIHandler : BaseHandler {
    public override void HandleRequest(string request) {
        if (request == "UI_CLICK") {
            Debug.Log("UI Handler: Обработка клика по UI");
        } else {
            base.HandleRequest(request);
        }
    }
}

public class GameplayHandler : BaseHandler {
    public override void HandleRequest(string request) {
        if (request == "PLAYER_MOVE") {
            Debug.Log("Gameplay Handler: Обработка движения игрока");
        } else {
            base.HandleRequest(request);
        }
    }
}

// Использование
public class InputManager : MonoBehaviour {
    private IHandler handlerChain;

    private void Start() {
        // Настройка цепочки
        var uiHandler = new UIHandler();
        var gameplayHandler = new GameplayHandler();
        
        uiHandler.SetNext(gameplayHandler);
        handlerChain = uiHandler;
    }

    private void Update() {
        // Пример: обработка различных типов ввода
        if (Input.GetMouseButtonDown(0)) {
            handlerChain.HandleRequest("UI_CLICK");
        }
        if (Input.GetKeyDown(KeyCode.W)) {
            handlerChain.HandleRequest("PLAYER_MOVE");
        }
    }
}
```

#### Подробный пример: система обработки ввода
Полный пример реализации надёжной системы обработки ввода с использованием паттерна «Цепочка обязанностей». Эта реализация демонстрирует, как:
- Обрабатывать различные типы ввода (UI, геймплей, катсцены)
- Обрабатывать ввод в зависимости от состояния игры
- Соединять несколько обработчиков в цепочку

👉 [Посмотреть полную реализацию](https://github.com/ozankasikci/unity-cheat-sheet/tree/master/Patterns/ChainOfResponsibility/README.md)

