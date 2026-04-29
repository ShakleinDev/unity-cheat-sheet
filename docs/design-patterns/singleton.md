# Unity Паттерн Синглтон — Глобальный доступ к экземпляру

```csharp
// Определяем класс синглтона
public class SingletonClass: MonoBehaviour {
    private static SingletonClass instance;

    public static SingletonClass Instance { get { return instance; } }

    private void Awake() {
        if (instance != null && instance != this) {
            Destroy(this.gameObject);
        } else {
            instance = this;
        }
    }

    public void SomeFunction() {
    }
}

// Используем синглтон в другом классе
public class AnotherClass: MonoBehaviour {

    private void Awake() {
       SingletonClass.Instance.SomeFunction();
    }
}
```

