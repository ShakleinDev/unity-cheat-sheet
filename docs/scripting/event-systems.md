# Unity Events — UnityEvent и делегаты C#

Unity предоставляет несколько способов обработки событий в играх. Ниже представлены основные подходы:

#### UnityEvents
UnityEvents — это сериализуемые события, которые можно настраивать в инспекторе и использовать в скриптах.

```csharp
using UnityEngine;
using UnityEngine.Events;

// Базовый UnityEvent
public class BasicEventExample : MonoBehaviour {
    // Отображается в инспекторе
    public UnityEvent onGameStart;
    
    void Start() {
        // Вызов события
        onGameStart?.Invoke();
    }
}

// UnityEvent с параметрами
[System.Serializable]
public class ScoreEvent : UnityEvent<int> { }

public class ParameterizedEventExample : MonoBehaviour {
    public ScoreEvent onScoreChanged;
    private int score = 0;

    public void AddScore(int points) {
        score += points;
        onScoreChanged?.Invoke(score);
    }
}
```

#### События и делегаты C#
Стандартные события C# предоставляют более программный подход к обработке событий.

Делегаты — это типобезопасные указатели на функции, а события — способ рассылать сообщения сразу нескольким подписчикам.

```csharp
public class GameEvents : MonoBehaviour {
    // Определение делегатов
    public delegate void GameStateHandler();
    public delegate void ScoreHandler(int newScore);

    // Объявление событий
    public static event GameStateHandler OnGameStart;
    public static event GameStateHandler OnGameOver;
    public static event ScoreHandler OnScoreChanged;

    // Методы для вызова событий
    public static void TriggerGameStart() {
        OnGameStart?.Invoke();
    }

    public static void TriggerGameOver() {
        OnGameOver?.Invoke();
    }

    public static void TriggerScoreChanged(int newScore) {
        OnScoreChanged?.Invoke(newScore);
    }
}

// Пример использования в другом классе
public class Player : MonoBehaviour {
    void OnEnable() {
        // Подписка на события
        GameEvents.OnGameStart += HandleGameStart;
        GameEvents.OnGameOver += HandleGameOver;
    }

    void OnDisable() {
        // Отписка от событий
        GameEvents.OnGameStart -= HandleGameStart;
        GameEvents.OnGameOver -= HandleGameOver;
    }

    private void HandleGameStart() {
        Debug.Log("Игра началась!");
    }

    private void HandleGameOver() {
        Debug.Log("Игра окончена!");
    }
}
```

