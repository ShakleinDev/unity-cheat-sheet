# Unity Паттерн Наблюдатель — Событийная Архитектура

```csharp
// Интерфейс наблюдателя
public interface IObserver {
    void UpdateObserver(string message);
}

// Конкретная реализация наблюдателя
public class ConcreteObserver : IObserver {
    private string name;

    public ConcreteObserver(string name) {
        this.name = name;
    }

    public void UpdateObserver(string message) {
        Debug.Log($"{name} получил сообщение: {message}");
    }
}

// Класс субъекта
public class Subject {
    private List<IObserver> observers = new List<IObserver>();

    public void AddObserver(IObserver observer) {
        observers.Add(observer);
    }

    public void RemoveObserver(IObserver observer) {
        observers.Remove(observer);
    }

    public void NotifyObservers(string message) {
        foreach (var observer in observers) {
            observer.UpdateObserver(message);
        }
    }
}

// Пример использования
public class ObserverExample : MonoBehaviour {
    private void Start() {
        Subject subject = new Subject();

        ConcreteObserver observer1 = new ConcreteObserver("Наблюдатель 1");
        ConcreteObserver observer2 = new ConcreteObserver("Наблюдатель 2");

        subject.AddObserver(observer1);
        subject.AddObserver(observer2);

        // Уведомить всех наблюдателей
        subject.NotifyObservers("Привет, наблюдатели!");
    }
}

```

