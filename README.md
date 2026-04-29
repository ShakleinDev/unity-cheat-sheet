# Unity Шпаргалка

## Содержание

- [Основы](#основы)
  - [MonoBehaviour](#monobehaviour)
  - [Transform](#transform)
  - [Vector3](#vector3)
  - [Quaternion](#quaternion)
  - [Углы Эйлера](#углы-эйлера)
- [Движение и вращение](#движение-и-вращение)
  - [Перемещение объекта](#перемещение-объекта)
    - [Transform.Translate()](#transformtranslate)
    - [Vector3.MoveTowards()](#vector3movetowards)
    - [Vector3.Lerp()](#vector3lerp)
    - [Vector3.SmoothDamp()](#vector3smoothdamp)
  - [Вращение объекта](#вращение-объекта)
    - [Transform.rotation](#transformrotation)
    - [Transform.eulerAngles](#transformeulerangles)
    - [Transform.Rotate()](#transformrotate)
    - [Transform.RotateAround()](#transformrotatearound)
    - [Transform.LookAt()](#transformlookat)
    - [Quaternion.LookRotation()](#quaternionlookrotation)
    - [Quaternion.FromToRotation()](#quaternionfromtorotation)
    - [Quaternion.ToAngleAxis()](#quaterniontoangleaxis)
- [Физика](#физика)
  - [Raycast](#raycast)
  - [Коллизии и триггеры](#коллизии-и-триггеры)
  - [Игнорирование коллизий](#игнорирование-коллизий)
- [Ввод](#ввод)
  - [Клавиатура](#клавиатура)
  - [Мышь](#мышь)
  - [Тач](#тач)
  - [Новая система ввода](#новая-система-ввода)
- [UI](#ui)
  - [Кнопка](#button)
  - [Ползунок](#slider)
- [Аудио](#аудио)
  - [Базовое воспроизведение аудио](#базовое-воспроизведение-аудио)
- [Скриптинг](#скриптинг)
  - [Корутины](#корутины)
  - [Async/Await](#asyncawait)
    - [Базовая структура](#базовая-структура)
    - [Загрузка ресурсов](#загрузка-ресурсов)
    - [Веб-запросы](#веб-запросы)
    - [Загрузка сцен](#загрузка-сцен)
    - [Параллельные операции](#параллельные-операции)
    - [Обработка таймаута](#обработка-таймаута)
    - [Лучшие практики](#лучшие-практики)
  - [Системы событий](#системы-событий)
  - [Скриптовые объекты](#скриптовые-объекты)
  - [Пользовательские скрипты редактора](#пользовательские-скрипты-редактора)
- [Паттерны проектирования](#паттерны-проектирования)
  - [Singleton](#singleton)
  - [Фабричный метод (Factory Pattern)](#фабричный-метод-factory-pattern)
  - [Наблюдатель (Observer Pattern)](#наблюдатель-observer-pattern)
  - [Команда (Command Pattern)](#команда-command-pattern)
  - [Паттерн «Состояние» (State Pattern)](#паттерн-состояние-state-pattern)
    - [Базовый пример](#базовый-пример)
    - [Подробный пример — система онбординга](#подробный-пример-система-онбординга)
  - [Паттерн «Стратегия» (Strategy Pattern)](#паттерн-стратегия-strategy-pattern)
    - [Базовый пример](#базовый-пример-1)
    - [Подробный пример — боевая система](#подробный-пример-боевая-система)
  - [Пул объектов (Object Pooling Pattern)](#пул-объектов-object-pooling-pattern)
  - [Цепочка обязанностей (Chain of Responsibility Pattern)](#цепочка-обязанностей-chain-of-responsibility-pattern)
    - [Базовый пример](#базовый-пример-2)
    - [Подробный пример — система обработки ввода](#подробный-пример-система-обработки-ввода)
- [Горячие клавиши](#горячие-клавиши)
  - [Редактирование в окне сцены](#редактирование-в-окне-сцены)
  - [Навигация в окне сцены](#навигация-в-окне-сцены)
  - [Управление иерархией](#управление-иерархией)
  - [Разметка](#разметка)
- [Практические примеры](#практические-примеры)
  - [Проверка приземления объекта](#проверка-приземления-объекта)
  - [Получение трансформа кости тела](#получение-трансформа-кости-тела)
  - [Поворот объекта лицом к камере](#поворот-объекта-лицом-к-камере)
  - [Следование и облёт камеры](#следование-и-облёт-камеры)
  - [Затухание UI-элемента](#затухание-ui-элемента)
  - [Загрузка следующей сцены](#загрузка-следующей-сцены)

## Основы

### [MonoBehaviour](https://docs.unity3d.com/ScriptReference/MonoBehaviour.html)
[Схема жизненного цикла MonoBehaviour](https://docs.unity3d.com/uploads/Main/monobehaviour_flowchart.svg)
```csharp
// MonoBehaviour — базовый класс, от которого наследуется каждый скрипт Unity.
// Предоставляет функции жизненного цикла, упрощающие разработку игры.

// Наиболее часто используемые из них:
Awake()
Start()
Update()
FixedUpdate()
LateUpdate()
OnGUI()
OnEnable()
OnDisable()
```

### [Transform](https://docs.unity3d.com/ScriptReference/Transform.html)
```csharp
// Transform — фундаментальный компонент Unity, присутствующий у каждого GameObject.
// Определяет позицию, вращение и масштаб объекта в игровом мире.

// Получение компонента Transform
Transform transform = gameObject.transform;

// Position: Vector3, представляющий координаты в мировом пространстве (x, y, z)
transform.position = new Vector3(0, 2, 0);      // Установить абсолютную позицию
transform.localPosition = Vector3.zero;         // Позиция относительно родителя

// Rotation: задаётся через углы Эйлера (градусы) или кватернионы
transform.rotation = Quaternion.identity;       // Без вращения
transform.eulerAngles = new Vector3(0, 90, 0);  // Поворот на 90 градусов вокруг оси Y
transform.localRotation = Quaternion.identity;  // Вращение относительно родителя

// Scale: Vector3, представляющий масштаб по каждой оси
transform.localScale = Vector3.one;             // Обычный масштаб (1, 1, 1)
transform.localScale = new Vector3(2, 2, 2);    // Двойной размер по всем осям

// Отношения родитель-потомок
transform.parent = anotherObject.transform;     // Установить родителя
transform.SetParent(null);                      // Убрать родителя (сделать корневым)
```

### [Vector3](https://docs.unity3d.com/ScriptReference/Vector3.html)
```csharp
// Vector3 — представление трёхмерных векторов и точек
// Используется для представления 3D-позиций по осям x, y и z.

Vector3 v = new Vector3(0f, 0f, 0f);
```

### [Quaternion](https://docs.unity3d.com/ScriptReference/Quaternion.html)
```csharp
// Quaternion хранит вращение Transform в мировом пространстве.
// Кватернионы основаны на комплексных числах и не подвержены шарнирному замку.
// Unity внутренне использует кватернионы для представления всех вращений.
// Обращаться к отдельным компонентам кватерниона (x,y,z,w) почти никогда не нужно; 

// Поворот на 30 градусов вокруг оси Y
Quaternion rotation = Quaternion.Euler(0, 30, 0);
```

### Углы Эйлера
```csharp
// Углы Эйлера — это градусные углы, например 90, 180, 45, 30 градусов.
// Кватернионы отличаются от углов Эйлера тем, что представляют точку на единичной сфере (радиус = 1).

// Создать кватернион: 30 градусов по X, 10 градусов по Y
Quaternion rotation = Quaternion.Euler(30, 10, 0);

// Использование вектора
Vector3 EulerRotation = new Vector3(30, 10, 0);
Quaternion rotation = Quaternion.Euler(EulerRotation);

// Преобразование кватерниона Transform в углы Эйлера
Quaternion quaternionAngles = transform.rotation;
Vector3 eulerAngles = quaternionAngles.eulerAngles;
```

## Движение и вращение

### Перемещение объекта
#### Transform.Translate()
```csharp
// Перемещает Transform в заданном направлении на заданное расстояние.
public void Translate(Vector3 translation);
public void Translate(Vector3 translation, Space relativeTo = Space.Self);

transform.Translate(Vector3.right * movementSpeed);
```

#### Vector3.MoveTowards()
```csharp
// Вычислить позицию между текущей и целевой точками
// не превышая расстояние, заданное параметром maxDistanceDelta
public static Vector3 MoveTowards(Vector3 current, Vector3 target, float maxDistanceDelta);

Vector3 targetPosition;
transform.position = Vector3.MoveTowards(transform.position, targetPosition, Time.deltaTime);
```

#### Vector3.Lerp()
```csharp
// Линейная интерполяция между двумя точками. Обеспечивает плавный переход.
public static Vector3 Lerp(Vector3 startValue, Vector3 endValue, float interpolationRatio);

Vector3 targetPosition;
float t = 0;
t += Time.deltaTime * speed;
transform.position = Vector3.Lerp(transform.position, targetPosition, t);
```

#### Vector3.SmoothDamp()
```csharp
// Постепенно изменяет вектор в направлении желаемой цели со временем.
// Вектор сглаживается функцией пружинного демпфера без перерегулирования.
// Чаще всего используется для плавного следования камеры.
public static Vector3 SmoothDamp(Vector3 current, Vector3 target, ref Vector3 currentVelocity, float smoothTime, float maxSpeed = Mathf.Infinity, float deltaTime = Time.deltaTime);

float smoothTime = 1f;
Vector3 velocity;
Vector3 targetPosition = target.TransformPoint(new Vector3(0, 5, -10));
// Плавно перемещать камеру к целевой позиции
transform.position = Vector3.SmoothDamp(transform.position, targetPosition, ref velocity, smoothTime);
```

### Вращение объекта
#### Transform.rotation
```csharp
// Quaternion хранит вращение Transform в мировом пространстве.
// Кватернионы основаны на комплексных числах и не подвержены шарнирному замку.
// Unity внутренне использует кватернионы для представления всех вращений.

transform.rotation = new Quaternion(rotx, roty, rotz, rotw);
```

#### Transform.eulerAngles
```csharp
// Transform.eulerAngles represents rotation in world space. 
// It is important to understand that although you are providing X, Y, and Z rotation values to describe your rotation
// those values are not stored in the rotation. Instead, the X, Y & Z values are converted to the Quaternion's internal format.

transform.eulerAngles = Vector3(rotx, roty, rotz);
```

#### Transform.Rotate()
```csharp
// Applies rotation around all the given axes.
public void Rotate(Vector3 eulers, Space relativeTo = Space.Self);
public void Rotate(float xAngle, float yAngle, float zAngle, Space relativeTo = Space.Self);

transform.Rotate(rotx, roty, rotz);
```

#### Transform.RotateAround()
```csharp
// Rotates the transform about axis passing through point in world coordinates by angle degrees.
public void RotateAround(Vector3 point, Vector3 axis, float angle);

// Вращать объект вокруг цели со скоростью 20 градусов/секунду.
Transform target;
transform.RotateAround(target.position, Vector3.up, 20 * Time.deltaTime);
```

#### Transform.LookAt()
```csharp
// Направляет положительную ось 'Z' (вперёд) объекта на позицию в мировом пространстве.
public void LookAt(Transform target);
public void LookAt(Transform target, Vector3 worldUp = Vector3.up);

// Повернуть вектор «вперёд» объекта в направлении целевого Transform.
Transform target;
transform.LookAt(target);

// То же самое, но worldUp = Vector3.left поворачивает объект набок.
transform.LookAt(target, Vector3.left);
```

#### Quaternion.LookRotation()
```csharp
// Создаёт вращение с заданным направлением «вперёд» и «вверх».
public static Quaternion LookRotation(Vector3 forward, Vector3 upwards = Vector3.up);

// Следующий код поворачивает объект в направлении целевого объекта.
Vector3 direction = target.position - transform.position;
Quaternion rotation = Quaternion.LookRotation(direction);
transform.rotation = rotation;
```

#### Quaternion.FromToRotation()
```csharp
// Создаёт вращение (кватернион) от fromDirection до toDirection.
public static Quaternion FromToRotation(Vector3 fromDirection, Vector3 toDirection);

// Устанавливает вращение так, чтобы ось Y Transform совпадала с осью Z.
transform.rotation = Quaternion.FromToRotation(Vector3.up, transform.forward);
```

#### Quaternion.ToAngleAxis()
```csharp
// Преобразует вращение в представление «угол-ось» (углы в градусах).
// То есть извлекает угол и ось, которые представляет данный кватернион.
public void ToAngleAxis(out float angle, out Vector3 axis);

// Извлекает вращение «угол-ось» из вращения Transform
float angle = 0.0f;
Vector3 axis = Vector3.zero;
transform.rotation.ToAngleAxis(out angle, out axis);
```

## Физика
### Raycast

```csharp
void FixedUpdate() {
    // Битовый сдвиг индекса слоя (8) для получения битовой маски
    int layerMask = 1 << 8;

    // Лучи будут касаться только коллайдеров на слое 8.
    // Но нам нужно обнаруживать всё, кроме слоя 8. Оператор ~ инвертирует битовую маску.
    layerMask = ~layerMask;

    RaycastHit hit;
    // Проверяем пересечение луча с объектами, исключая слой игрока
    if (Physics.Raycast(transform.position, transform.TransformDirection(Vector3.forward), out hit, Mathf.Infinity, layerMask)) {
        Debug.DrawRay(transform.position, transform.TransformDirection(Vector3.forward) * hit.distance, Color.yellow);
        Debug.Log("Did Hit");
    }
}
```

### Коллизии и триггеры

> Полное руководство с матрицей коллизий, 2D-событиями и паттернами см. в [Коллизии и триггеры](docs/physics/collisions.md)

#### События коллизий (с физическим откликом)

```csharp
// Вызывается при начале столкновения
void OnCollisionEnter(Collision collision)
{
    Debug.Log("Hit: " + collision.gameObject.name);
    
    // Доступ к деталям столкновения
    Vector3 impactVelocity = collision.relativeVelocity;
    ContactPoint contact = collision.GetContact(0);
    Vector3 hitPoint = contact.point;
}

// Вызывается каждый кадр во время столкновения
void OnCollisionStay(Collision collision) { }

// Вызывается при завершении столкновения
void OnCollisionExit(Collision collision) { }
```

#### События триггеров (без физического отклика)

```csharp
// Вызывается при входе в триггер (требуется флаг "Is Trigger" на коллайдере)
void OnTriggerEnter(Collider other)
{
    if (other.CompareTag("Player"))
    {
        // Подобрать предмет, войти в зону и т.д.
        Destroy(gameObject);
    }
}

// Вызывается каждый кадр, пока объект находится внутри триггера
void OnTriggerStay(Collider other) { }

// Вызывается при выходе из триггера
void OnTriggerExit(Collider other) { }
```

#### 2D-эквиваленты
```csharp
void OnCollisionEnter2D(Collision2D collision) { }
void OnTriggerEnter2D(Collider2D other) { }
// Also: OnCollisionStay2D, OnCollisionExit2D, OnTriggerStay2D, OnTriggerExit2D
```

#### Требования
- Оба объекта должны иметь **Collider** (или Collider2D)
- Хотя бы один объект должен иметь **Rigidbody** (или Rigidbody2D)
- Для триггеров: включите **"Is Trigger"** хотя бы на одном коллайдере

### Игнорирование коллизий
```csharp
// Заставляет систему коллизий игнорировать все столкновения между collider1 и collider2.
public static void IgnoreCollision(Collider collider1, Collider collider2, bool ignore = true);

// Отключаем обнаружение коллизий между коллайдерами объектов ally и bullet.
Transform bullet;
Transform ally;
Physics.IgnoreCollision(bullet.GetComponent<Collider>(), ally.GetComponent<Collider>());
```

## Ввод

### Клавиатура

```csharp
// Возвращает true в кадре, когда пользователь нажимает клавишу
if (Input.GetKeyDown(KeyCode.Space)) {
    Debug.Log("Space key was pressed");
}

// Jump также привязан к пробелу в Input Manager
if (Input.GetButtonDown("Jump")) {
    Debug.Log("Do something");
}
```

### Мышь

```csharp
if (Input.GetAxis("Mouse X") < 0) {
    Debug.Log("Mouse moved left");
}

if (Input.GetAxis("Mouse Y") > 0) {
    Debug.Log("Mouse moved up");
}

if (Input.GetMouseButtonDown(0)) {
    Debug.Log("Pressed primary button.");
}

if (Input.GetMouseButtonDown(1)) {
    Debug.Log("Pressed secondary button.");
}

if (Input.GetMouseButtonDown(2)) {
    Debug.Log("Pressed middle click.");
}
```

### Тач
```csharp
if (Input.touchCount > 0) {
    touch = Input.GetTouch(0);

    if (touch.phase == TouchPhase.Began) {
        Debug.Log("Touch began");
    }

    if (touch.phase == TouchPhase.Moved) {
        Debug.Log("Touch moves");
    }

    if (touch.phase == TouchPhase.Ended) {
        Debug.Log("Touch ended");
    }
}
```

### Новая система ввода

Пакет Input System является **стандартом для Unity 6** и рекомендуется для всех новых проектов. Обеспечивает гибкую обработку ввода с нескольких устройств и поддержку переназначения клавиш во время выполнения.

> Полное руководство со всеми примерами см. в [Новая система ввода](docs/input/new-input-system.md)

#### Быстрая настройка
```
Window > Package Manager > Unity Registry > Input System > Install
Edit > Project Settings > Player > Active Input Handling > Input System Package (New)
```

#### Прямой доступ к устройствам
```csharp
using UnityEngine.InputSystem;

// Keyboard
if (Keyboard.current.spaceKey.wasPressedThisFrame) {
    Debug.Log("Space pressed");
}

// Mouse
Vector2 mousePos = Mouse.current.position.ReadValue();
if (Mouse.current.leftButton.wasPressedThisFrame) {
    Debug.Log("Left click at " + mousePos);
}

// Gamepad (always check for null!)
if (Gamepad.current != null) {
    Vector2 leftStick = Gamepad.current.leftStick.ReadValue();
    if (Gamepad.current.buttonSouth.wasPressedThisFrame) {
        Debug.Log("A/Cross button pressed");
    }
}
```

#### Использование Input Actions (рекомендуется)
```csharp
using UnityEngine;
using UnityEngine.InputSystem;

public class PlayerController : MonoBehaviour
{
    [SerializeField] private InputActionReference moveAction;
    [SerializeField] private InputActionReference jumpAction;

    void OnEnable()
    {
        moveAction.action.Enable();
        jumpAction.action.Enable();
        jumpAction.action.performed += OnJump;
    }

    void OnDisable()
    {
        jumpAction.action.performed -= OnJump;
        moveAction.action.Disable();
        jumpAction.action.Disable();
    }

    void Update()
    {
        Vector2 move = moveAction.action.ReadValue<Vector2>();
        transform.Translate(move.x * Time.deltaTime, 0, move.y * Time.deltaTime);
    }

    void OnJump(InputAction.CallbackContext context)
    {
        Debug.Log("Jump!");
    }
}
```

## UI

### Button

```csharp
// Button используется для обработки кликов и взаимодействий пользователя.
// Прикрепите этот скрипт к компоненту Button для обработки нажатий.

using UnityEngine.UI;

Button myButton = GetComponent<Button>();
myButton.onClick.AddListener(MyButtonClickHandler);

void MyButtonClickHandler() {
    Debug.Log("Button Clicked!");
}
```

### Slider
```csharp
// Slider используется для выбора значения в заданном диапазоне.
// Прикрепите этот скрипт к компоненту Slider для обработки изменений значения.

using UnityEngine.UI;

Slider mySlider = GetComponent<Slider>();
mySlider.onValueChanged.AddListener(MySliderValueChangedHandler);

void MySliderValueChangedHandler(float value) {
    Debug.Log("Slider Value: " + value);
}
```

## Аудио

### Базовое воспроизведение аудио

```csharp
public class PlayAudio : MonoBehaviour {
    public AudioSource audioSource;

    void Start() {
        // Вызов Play на уже воспроизводящемся источнике звука перезапускает его с начала
        audioSource.Play();
    }
}
```

## Скриптинг

### Корутины
Корутины в Unity — мощная возможность, позволяющая приостановить выполнение функции и возобновить его позже. Особенно полезны для задач, которые нужно растянуть на несколько кадров: анимации, ожидание условия или асинхронные операции.

#### Базовый пример корутины
```csharp
using UnityEngine;
using System.Collections;

public class CoroutineExample : MonoBehaviour {
    void Start() {
        // Запустить корутину
        StartCoroutine(ExampleCoroutine());
    }

    IEnumerator ExampleCoroutine() {
        Debug.Log("Coroutine started");

        // Ожидать 2 секунды
        yield return new WaitForSeconds(2);

        Debug.Log("Coroutine resumed after 2 seconds");
    }
}
```

#### Использование корутин для повторяющихся действий
Корутины можно использовать для выполнения повторяющихся действий с задержкой между итерациями.

```csharp
IEnumerator RepeatActionCoroutine() {
    while (true) {
        Debug.Log("Action performed");
        
        // Ожидать 1 секунду перед следующим повторением
        yield return new WaitForSeconds(1);
    }
}

// Запустить корутину
StartCoroutine(RepeatActionCoroutine());
```

#### Ожидание условия
Корутины также могут ожидать выполнения условия перед продолжением.

```csharp
IEnumerator WaitForConditionCoroutine() {
    Debug.Log("Waiting for condition...");

    // Ожидать выполнения условия
    yield return new WaitUntil(() => SomeConditionIsTrue());

    Debug.Log("Condition met, resuming coroutine");
}

bool SomeConditionIsTrue() {
    // Замените реальным условием
    return Time.time > 5;
}

// Запустить корутину
StartCoroutine(WaitForConditionCoroutine());
```

#### Использование корутин с Unity Events
Корутины можно использовать для обработки событий во времени, например, плавного исчезновения UI-элемента.

```csharp
IEnumerator FadeOutCoroutine(CanvasGroup canvasGroup, float duration) {
    float startAlpha = canvasGroup.alpha;
    float rate = 1.0f / duration;
    float progress = 0.0f;

    while (progress < 1.0f) {
        canvasGroup.alpha = Mathf.Lerp(startAlpha, 0, progress);
        progress += rate * Time.deltaTime;

        yield return null; // Ожидать следующего кадра
    }

    canvasGroup.alpha = 0;
}

// Использование
CanvasGroup myCanvasGroup = GetComponent<CanvasGroup>();
StartCoroutine(FadeOutCoroutine(myCanvasGroup, 2.0f));
```

#### Остановка корутин
Остановить корутину можно с помощью `StopCoroutine()` или `StopAllCoroutines()`.

```csharp
Coroutine myCoroutine;

void Start() {
    myCoroutine = StartCoroutine(ExampleCoroutine());
}

void StopMyCoroutine() {
    if (myCoroutine != null) {
        StopCoroutine(myCoroutine);
    }
}

void StopAllMyCoroutines() {
    StopAllCoroutines();
}
```

#### Важные замечания
- Корутины — не потоки. Они выполняются в основном потоке с теми же ограничениями производительности.
- Используйте `yield return null;` для ожидания следующего кадра.
- Используйте `yield return new WaitForSeconds(seconds);` для ожидания заданного времени.
- Используйте `yield return new WaitUntil(() => condition);` для ожидания условия.
- Корутины можно вкладывать друг в друга и делать yield return из другой корутины.

### Системы событий
Unity предоставляет несколько способов обработки событий в играх. Вот основные подходы:

#### UnityEvents
UnityEvents — сериализуемые события, настраиваемые в Inspector и используемые в скриптах.

```csharp
using UnityEngine;
using UnityEngine.Events;

// Basic UnityEvent
public class BasicEventExample : MonoBehaviour {
    // Отображается в Inspector
    public UnityEvent onGameStart;
    
    void Start() {
        // Вызвать событие
        onGameStart?.Invoke();
    }
}

// UnityEvent with parameters
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

#### C# Events и Delegates
Традиционные события C# обеспечивают более программный подход к обработке событий.


Делегаты — типобезопасные указатели на функции, события — способ рассылки сообщений нескольким подписчикам.

```csharp
public class GameEvents : MonoBehaviour {
    // Определение делегата
    public delegate void GameStateHandler();
    public delegate void ScoreHandler(int newScore);

    // Объявление события
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

// Example usage in another class
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
        Debug.Log("Game Started!");
    }

    private void HandleGameOver() {
        Debug.Log("Game Over!");
    }
}
```

### Скриптовые объекты
```csharp
// ScriptableObjects — контейнеры данных для хранения больших объёмов данных, независимо от экземпляров классов.
[CreateAssetMenu(fileName = "NewData", menuName = "ScriptableObjects/Data")]
public class Data : ScriptableObject {
    public string dataName;
    public int dataValue;
}

// Использование
Data myData = ScriptableObject.CreateInstance<Data>();
```

### Пользовательские скрипты редактора
```csharp
// Пользовательские скрипты редактора позволяют создавать кастомные инспекторы и окна в Unity Editor.
using UnityEditor;
using UnityEngine;

[CustomEditor(typeof(MyComponent))]
public class MyComponentEditor : Editor {
    public override void OnInspectorGUI() {
        DrawDefaultInspector();

        MyComponent myComponent = (MyComponent)target;
        if (GUILayout.Button("Do Something")) {
            myComponent.DoSomething();
        }
    }
}
```

### Async/Await
Async/await — паттерн программирования, упрощающий асинхронный код. Позволяет писать асинхронный код, который выглядит и ведёт себя как синхронный. Особенно полезен для операций, требующих времени:
- Загрузка ресурсов
- Сетевые запросы
- Файловые операции
- Загрузка сцен

#### Базовая структура
```csharp
using UnityEngine;
using System.Threading.Tasks;
using System;

public class AsyncAwaitExample : MonoBehaviour
{
    // Базовая структура async/await в Unity
    private async void Start()
    {
        Debug.Log("Starting async operation");
        await Task.Delay(1000); // Wait for 1 second
        Debug.Log("Async operation completed");
    }

    // ВАЖНО: методы Update и MonoBehaviour должны возвращать void
    // Они не могут быть async Task
    private void Update() { }      // Правильно
    // private async Task Update() {} // Неправильно!
}
```

#### Загрузка ресурсов
```csharp
// Асинхронная загрузка ресурсов с отслеживанием прогресса
private async Task<Texture2D> LoadTextureAsync()
{
    var resourcePath = "Textures/MyTexture";
    var request = Resources.LoadAsync<Texture2D>(resourcePath);
    
    while (!request.isDone)
    {
        // Сообщить о прогрессе
        Debug.Log($"Loading: {request.progress * 100}%");
        await Task.Yield(); // Дать возможность продолжить другим операциям
    }

    return request.asset as Texture2D;
}
```

#### Веб-запросы
```csharp
// Выполнение веб-запросов с отслеживанием прогресса
private async Task<string> FetchDataAsync(string url)
{
    using var request = UnityWebRequest.Get(url);
    var operation = request.SendWebRequest();

    while (!operation.isDone)
    {
        Debug.Log($"Downloading: {request.downloadProgress * 100}%");
        await Task.Yield();
    }

    if (request.result != UnityWebRequest.Result.Success)
        throw new Exception($"Failed to fetch data: {request.error}");

    return request.downloadHandler.text;
}
```

#### Загрузка сцен
```csharp
// Асинхронная загрузка сцен с отслеживанием прогресса
private async Task LoadSceneAsync(string sceneName)
{
    var operation = SceneManager.LoadSceneAsync(sceneName);
    operation.allowSceneActivation = false; // Не активировать сразу

    while (operation.progress < 0.9f) // 0.9 — прогресс непосредственно перед активацией
    {
        Debug.Log($"Loading scene: {operation.progress * 100}%");
        await Task.Yield();
    }

    Debug.Log("Scene ready to activate");
    operation.allowSceneActivation = true;
}
```

#### Параллельные операции
```csharp
// Параллельный запуск нескольких асинхронных операций
private async Task LoadGameAssetsAsync()
{
    try
    {
        // Запустить несколько операций одновременно
        var textureTask = LoadTextureAsync();
        var dataTask = FetchDataAsync("https://api.example.com/gamedata");
        var sceneTask = LoadSceneAsync("Level1");

        // Дождаться завершения всех задач
        await Task.WhenAll(textureTask, dataTask, sceneTask);
        
        // Все ресурсы загружены
        Debug.Log("All assets loaded successfully");
    }
    catch (Exception e)
    {
        Debug.LogError($"Failed to load assets: {e.Message}");
    }
}
```

#### Обработка таймаута
```csharp
// Реализация таймаута для асинхронных операций
private async Task<T> WithTimeout<T>(Task<T> task, TimeSpan timeout)
{
    var timeoutTask = Task.Delay(timeout);
    var completedTask = await Task.WhenAny(task, timeoutTask);
    
    if (completedTask == timeoutTask)
        throw new TimeoutException("Operation timed out");
            
    return await task; // Извлечь результат или передать исключение дальше
}

// Complete example using timeout handling
private async Task InitializeGameAsync()
{
    try
    {
        // Показать экран загрузки
        ShowLoadingUI();

        // Запустить несколько операций загрузки с таймаутом
        var loadingTask = Task.WhenAll(
            LoadTextureAsync(),
            FetchDataAsync("https://api.example.com/gamedata"),
            LoadSceneAsync("MainLevel")
        );

        // Ждать завершения всех операций с таймаутом 30 секунд
        await WithTimeout(loadingTask, TimeSpan.FromSeconds(30));

        // Скрыть экран загрузки
        HideLoadingUI();
        Debug.Log("Game initialized successfully");
    }
    catch (TimeoutException)
    {
        Debug.LogError("Game initialization timed out");
        ShowRetryButton();
    }
    catch (Exception e)
    {
        Debug.LogError($"Failed to initialize game: {e.Message}");
        ShowErrorUI();
    }
}
```

#### Лучшие практики
При использовании async/await в Unity следуйте этим рекомендациям:

1. Всегда обрабатывайте исключения в async-методах
2. Используйте Task.Yield() вместо Task.Delay() для пофреймовых операций
3. Помните, что async void следует использовать только для обработчиков событий и методов MonoBehaviour
4. По возможности используйте CancellationToken для поддержки отмены
5. Реализуйте обработку таймаута для сетевых операций
6. Не блокируйте основной поток через .Result или .Wait()
7. Используйте Task.WhenAll для параллельных операций

## Паттерны проектирования
### Singleton

```csharp
// Определение класса Singleton
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

// Использование в другом классе
public class AnotherClass: MonoBehaviour {

    private void Awake() {
       SingletonClass.Instance.SomeFunction();
    }
}
```

### Фабричный метод (Factory Pattern)
```csharp
// Интерфейс врага
public interface IEnemy {
    void Attack();
    void TakeDamage(int damage);
}

// Конкретная реализация врага: Гоблин
public class Goblin : IEnemy {
    public void Attack() => Debug.Log("Goblin attacking!");
    public void TakeDamage(int damage) => Debug.Log($"Goblin taking {damage} damage.");
}

// Конкретная реализация врага: Орк
public class Orc : IEnemy {
    public void Attack() => Debug.Log("Orc attacking!");
    public void TakeDamage(int damage) => Debug.Log($"Orc taking {damage} damage.");
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

// Клиентский класс, использующий фабрику для создания и взаимодействия с врагами
public class GameManager : MonoBehaviour {
    private void Start() {
        InteractWithEnemy(new GoblinFactory());
        InteractWithEnemy(new OrcFactory());

        // Можно добавлять новые конкретные реализации IEnemy
        // не изменяя существующий клиентский код
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

### Наблюдатель (Observer Pattern)
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
        Debug.Log($"{name} received message: {message}");
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

        ConcreteObserver observer1 = new ConcreteObserver("Observer 1");
        ConcreteObserver observer2 = new ConcreteObserver("Observer 2");

        subject.AddObserver(observer1);
        subject.AddObserver(observer2);

        // Уведомить всех наблюдателей
        subject.NotifyObservers("Hello Observers!");
    }
}

```

### Команда (Command Pattern)
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

// Класс инициатора
public class CommandInvoker {
    private Stack<ICommand> commandStack = new Stack<ICommand>();

    public void ExecuteCommand(ICommand command) {
        commandStack.Push(command);
        command.Execute();
    }

    public void Undo() {
        if (commandStack.Count > 0) {
            var command = commandStack.Pop();
            // Реализовать метод отмены при необходимости
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

        // Добавьте другие направления и invoker.Undo() для отмены
    }
}
```

### Паттерн «Состояние» (State Pattern)
Паттерн «Состояние» позволяет объекту изменять своё поведение при изменении внутреннего состояния. Инкапсулирует поведение, специфичное для каждого состояния, и делает переходы между ними явными.

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
    public void Enter() => Debug.Log("Entered Idle State");
    public void Update() => Debug.Log("Updating Idle State");
    public void Exit() => Debug.Log("Exited Idle State");
}

// Машина состояний, управляющая переходами
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

#### Подробный пример — система онбординга
Полный пример реализации системы онбординга/туториала с использованием паттерна «Состояние». Демонстрирует:
- Управление различными состояниями туториала (движение, бой, инвентарь)
- Обработку переходов между состояниями
- Отслеживание прогресса игрока в туториале

👉 [Посмотреть полную реализацию](Patterns/StatePattern/README.md)

### Паттерн «Стратегия» (Strategy Pattern)
Паттерн «Стратегия» определяет семейство алгоритмов, инкапсулирует каждый из них и делает их взаимозаменяемыми. Позволяет алгоритму изменяться независимо от клиентов, которые его используют.

#### Базовый пример
```csharp
// Интерфейс стратегии
public interface IStrategy {
    void Execute();
}

// Пример реализации стратегии
public class AttackStrategy : IStrategy {
    public void Execute() => Debug.Log("Performing attack!");
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

#### Подробный пример — боевая система
Полный пример реализации гибкой боевой системы с использованием паттерна «Стратегия». Демонстрирует:
- Определение различных стратегий атаки (ближний бой, дальний, площадная)
- Переключение между стратегиями во время выполнения
- Инкапсуляцию боевых алгоритмов

👉 [Посмотреть полную реализацию](Patterns/StrategyPattern/README.md)

### Пул объектов (Object Pooling Pattern)
```csharp
using System.Collections.Generic;
using UnityEngine;

public class ObjectPool : MonoBehaviour
{
    [System.Serializable]
    public class Pool
    {
        public string tag;
        public GameObject prefab;
        public int size;
    }

    public List<Pool> pools;
    public Dictionary<string, Queue<GameObject>> poolDictionary;

    private void Start()
    {
        poolDictionary = new Dictionary<string, Queue<GameObject>>();

        foreach (Pool pool in pools)
        {
            Queue<GameObject> objectPool = new Queue<GameObject>();

            for (int i = 0; i < pool.size; i++)
            {
                GameObject obj = Instantiate(pool.prefab);
                obj.SetActive(false);
                objectPool.Enqueue(obj);
            }

            poolDictionary.Add(pool.tag, objectPool);
        }
    }

    public GameObject SpawnFromPool(string tag, Vector3 position, Quaternion rotation)
    {
        if (!poolDictionary.ContainsKey(tag))
        {
            Debug.LogWarning("Pool with tag " + tag + " doesn't exist.");
            return null;
        }

        GameObject objectToSpawn = poolDictionary[tag].Dequeue();

        objectToSpawn.SetActive(true);
        objectToSpawn.transform.position = position;
        objectToSpawn.transform.rotation = rotation;

        poolDictionary[tag].Enqueue(objectToSpawn);

        return objectToSpawn;
    }
}

// Использование example
public class GameManager : MonoBehaviour
{
    public ObjectPool objectPool;

    private void Update()
    {
        if (Input.GetKeyDown(KeyCode.Space))
        {
            objectPool.SpawnFromPool("Bullet", transform.position, Quaternion.identity);
        }
    }
}
```

### Цепочка обязанностей (Chain of Responsibility Pattern)
Паттерн «Цепочка обязанностей» создаёт цепочку объектов-обработчиков для запроса. Каждый обработчик содержит ссылку на следующий и решает — обработать запрос самостоятельно или передать дальше.

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
            Debug.Log("UI Handler: Handling UI click");
        } else {
            base.HandleRequest(request);
        }
    }
}

public class GameplayHandler : BaseHandler {
    public override void HandleRequest(string request) {
        if (request == "PLAYER_MOVE") {
            Debug.Log("Gameplay Handler: Handling player movement");
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

#### Подробный пример — система обработки ввода
Полный пример реализации надёжной системы обработки ввода с использованием паттерна «Цепочка обязанностей». Демонстрирует:
- Обработку различных типов ввода (UI, геймплей, катсцены)
- Обработку ввода в зависимости от состояния игры
- Объединение нескольких обработчиков в цепочку

👉 [Посмотреть полную реализацию](Patterns/ChainOfResponsibility/README.md)

## Горячие клавиши

### Редактирование в окне сцены
```
Q - Pan tool
W - Move tool
E - Rotate tool
R - Scale tool
T - Rect tool
Y - Transform tool

CTRL/CMD + Z - Undo
CTRL/CMD + Y - Redo
CTRL/CMD + S - Save
CTRL/CMD + P - Play/Stop
CTRL/CMD + SHIFT + P - Pause
```

### Навигация в окне сцены
```
Alt + Left Click - Orbit around scene view pivot
Alt + Right Click - Zoom in/out
Middle Mouse - Orbit
Middle Mouse + Alt - Zoom
F - Focus on selected object
CTRL/CMD + ALT + F - Frame selected
Hold Right Click - Free look (FPS style)
```

### Управление иерархией
```
CTRL/CMD + SHIFT + N - Create empty GameObject
ALT + SHIFT + N - Create empty child GameObject
CTRL/CMD + D - Duplicate selected
CTRL/CMD + ALT + F - Frame selected object
F2 - Rename selected
Delete - Delete selected
```

### Разметка
```
CTRL/CMD + 1-5 - Switch between layouts
SHIFT + Space - Toggle maximize current window
```

## Практические примеры

### Проверка приземления объекта

```csharp
RaycastHit hit;

// В отличие от этого примера, обычно следует передавать layerMask для обнаружения только земли
if (Physics.Raycast(transform.position, Vector3.down, out hit, 0.5f)) {
   Debug.log("Hit something below!");
}
```

### Получение трансформа кости тела

```csharp
Animator animator;

Transform transform = animator.GetBoneTransform(HumanBodyBones.Head);
```

### Поворот объекта лицом к камере

```csharp
var camPosition = Camera.main.transform.position;

transform.rotation = Quaternion.LookRotation(transform.position - camPosition);
```

### Следование и облёт камеры

[Полный пример](docs/practical-use-cases/camera-follow-orbit.md) — плавное следование через `Vector3.SmoothDamp` с орбитальным вращением по входным данным рысканья/тангажа.

```csharp
// Накапливать ввод по рысканью/тангажу
yaw += lookInput.x * orbitSpeed * Time.deltaTime;
pitch = Mathf.Clamp(pitch - lookInput.y * orbitSpeed * Time.deltaTime, pitchLimits.x, pitchLimits.y);

// Построить вращение и разместить камеру
Quaternion rotation = Quaternion.AngleAxis(yaw, Vector3.up) * Quaternion.AngleAxis(pitch, Vector3.right);
Vector3 desiredPosition = target.position + rotation * followOffset;
transform.position = Vector3.SmoothDamp(transform.position, desiredPosition, ref velocity, followSmoothTime);
transform.rotation = Quaternion.LookRotation(target.position - transform.position, Vector3.up);
```

### Затухание UI-элемента

[Полный пример](docs/practical-use-cases/fade-ui-element.md) — управляемое корутиной затухание `CanvasGroup` для подсказок и панелей HUD.

```csharp
IEnumerator Fade(CanvasGroup group, float targetAlpha, float duration)
{
    float start = group.alpha;
    float time = 0f;
    while (time < duration)
    {
        time += Time.unscaledDeltaTime;
        group.alpha = Mathf.Lerp(start, targetAlpha, time / duration);
        yield return null;
    }
    group.alpha = targetAlpha;
}
```

### Загрузка следующей сцены

```csharp
var nextSceneToLoad = SceneManager.GetActiveScene().buildIndex + 1;
var totalSceneCount = SceneManager.sceneCountInBuildSettings;

if (nextSceneToLoad < totalSceneCount) {
  SceneManager.LoadScene(nextSceneToLoad);
}
```

