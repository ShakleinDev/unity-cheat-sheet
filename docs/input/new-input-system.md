# Unity Новая система ввода — Полное руководство

Пакет Input System предоставляет современный и гибкий способ обработки пользовательского ввода на всех платформах. Это **стандарт для Unity 6** и рекомендуется для всех новых проектов.

## Устаревший и новый менеджер ввода

| Возможность | Устаревший (`UnityEngine.Input`) | Новый (`UnityEngine.InputSystem`) |
|---------|------------------------------|----------------------------------|
| Поддержка нескольких устройств | Ограниченная | Отличная |
| Переназначение клавиш во время выполнения | Ручная реализация | Встроенная поддержка |
| Ввод на основе действий | Нет | Да |
| Типобезопасность | На основе строк | Генерируемые C# классы |
| По умолчанию в Unity 6 | Нет | Да |
| Локальный мультиплеер | Сложная реализация | Встроенный PlayerInputManager |

## Установка и настройка

### Проекты на Unity 6
Input System устанавливается по умолчанию. Проверьте в **Edit > Project Settings > Player > Active Input Handling**, что выбрано **Input System Package (New)** или **Both**.

### Более ранние версии Unity
```
Window > Package Manager > Unity Registry > Input System > Install
```

После установки Unity предложит включить новый бэкенд ввода. Нажмите **Yes**, и Unity перезапустится.

### Необходимый using
```csharp
using UnityEngine.InputSystem;
```

---

## Прямой доступ к устройствам

Прямой доступ к устройствам позволяет считывать ввод немедленно, без настройки действий (Input Actions). Подходит для прототипирования или простых проектов.

### Клавиатура

```csharp
// Проверить, удерживается ли клавиша прямо сейчас
if (Keyboard.current.wKey.isPressed)
{
    // Клавиша W удерживается
}

// Проверить, была ли клавиша нажата в этом кадре
if (Keyboard.current.spaceKey.wasPressedThisFrame)
{
    // Пробел только что нажат
}

// Проверить, была ли клавиша отпущена в этом кадре
if (Keyboard.current.escapeKey.wasReleasedThisFrame)
{
    // Escape только что отпущен
}

// Проверить, нажата ли хоть какая-нибудь клавиша
if (Keyboard.current.anyKey.isPressed)
{
    // Какая-то клавиша нажата
}
```

### Мышь

```csharp
// Позиция мыши (в экранных координатах)
Vector2 mousePos = Mouse.current.position.ReadValue();

// Смещение мыши с последнего кадра
Vector2 mouseDelta = Mouse.current.delta.ReadValue();

// Колесо прокрутки (y = вертикальная прокрутка)
Vector2 scroll = Mouse.current.scroll.ReadValue();
float scrollY = scroll.y;

// Кнопки мыши
if (Mouse.current.leftButton.isPressed) { }
if (Mouse.current.leftButton.wasPressedThisFrame) { }
if (Mouse.current.rightButton.wasPressedThisFrame) { }
if (Mouse.current.middleButton.isPressed) { }

// Боковые кнопки
if (Mouse.current.forwardButton.wasPressedThisFrame) { }
if (Mouse.current.backButton.wasPressedThisFrame) { }
```

### Геймпад

```csharp
// Всегда проверяйте, подключён ли геймпад
if (Gamepad.current == null) return;

// Левый стик (Vector2, от -1 до 1)
Vector2 leftStick = Gamepad.current.leftStick.ReadValue();

// Правый стик
Vector2 rightStick = Gamepad.current.rightStick.ReadValue();

// Отдельные оси стика
float leftX = Gamepad.current.leftStick.x.ReadValue();
float leftY = Gamepad.current.leftStick.y.ReadValue();

// Триггеры (от 0 до 1)
float leftTrigger = Gamepad.current.leftTrigger.ReadValue();
float rightTrigger = Gamepad.current.rightTrigger.ReadValue();

// Крестовина (D-pad) как Vector2
Vector2 dpad = Gamepad.current.dpad.ReadValue();

// Лицевые кнопки (A/B/X/Y на Xbox, Крест/Круг/Квадрат/Треугольник на PlayStation)
if (Gamepad.current.buttonSouth.wasPressedThisFrame) { } // A / Крест
if (Gamepad.current.buttonEast.wasPressedThisFrame) { }  // B / Круг
if (Gamepad.current.buttonWest.wasPressedThisFrame) { }  // X / Квадрат
if (Gamepad.current.buttonNorth.wasPressedThisFrame) { } // Y / Треугольник

// Шифт-кнопки
if (Gamepad.current.leftShoulder.wasPressedThisFrame) { }  // LB / L1
if (Gamepad.current.rightShoulder.wasPressedThisFrame) { } // RB / R1

// Нажатие стиков
if (Gamepad.current.leftStickButton.wasPressedThisFrame) { }  // L3
if (Gamepad.current.rightStickButton.wasPressedThisFrame) { } // R3

// Кнопки меню
if (Gamepad.current.startButton.wasPressedThisFrame) { }
if (Gamepad.current.selectButton.wasPressedThisFrame) { }
```

### Пример движения с геймпадом

```csharp
public class GamepadMovement : MonoBehaviour
{
    public float moveSpeed = 5f;
    public float turnSpeed = 90f;

    void Update()
    {
        if (Gamepad.current == null) return;

        // Движение с помощью левого стика
        Vector2 move = Gamepad.current.leftStick.ReadValue();
        transform.Translate(move.x * moveSpeed * Time.deltaTime, 0, move.y * moveSpeed * Time.deltaTime);

        // Поворот с помощью правого стика
        Vector2 look = Gamepad.current.rightStick.ReadValue();
        transform.Rotate(0, look.x * turnSpeed * Time.deltaTime, 0);
    }
}
```

### Сенсорный экран

```csharp
if (Touchscreen.current == null) return;

// Основное касание
var touch = Touchscreen.current.primaryTouch;

if (touch.press.isPressed)
{
    Vector2 position = touch.position.ReadValue();
    Vector2 delta = touch.delta.ReadValue();
}

// Все касания
foreach (var t in Touchscreen.current.touches)
{
    if (t.press.isPressed)
    {
        Vector2 pos = t.position.ReadValue();
    }
}
```

---

## Действия ввода (Input Actions)

Действия ввода создают уровень абстракции между вашим кодом и физическими устройствами ввода. Это **рекомендуемый подход** для готовых игр.

### Преимущества
- Один код работает для клавиатуры, геймпада и сенсорного экрана
- Игроки могут переназначать управление во время выполнения
- Легче управлять сложными схемами управления
- Типобезопасность с помощью генерируемых C# классов

### Создание ассета Input Actions

1. **Создать ассет**: Правой кнопкой мыши в Project > Create > Input Actions
2. **Открыть редактор**: Двойной щелчок по файлу `.inputactions`
3. **Создать Action Map**: например, "Gameplay", "UI", "Vehicle"
4. **Добавить действия**: например, "Move", "Jump", "Fire"
5. **Добавить привязки**: назначить клавиатуру, мышь, геймпад
6. **Генерация C# класса**: отметьте "Generate C# Class" в инспекторе ассета
7. **Применить**: нажмите "Apply" для сохранения изменений

### Типы действий

| Тип | Применение | Пример |
|------|----------|---------|
| **Value** | Непрерывный ввод | Стик движения, позиция мыши |
| **Button** | Дискретное нажатие/отпускание | Прыжок, выстрел, взаимодействие |
| **Pass-Through** | Весь ввод без обработки | Сырой ввод с устройства |

### Составные привязки

Объединение нескольких входов в одно значение (например, WASD → Vector2):

1. Добавьте действие типа **Value** с типом управления **Vector2**
2. Добавьте привязку > **2D Vector Composite**
3. Назначьте: Up=W, Down=S, Left=A, Right=D
4. Добавьте ещё одну привязку для **Gamepad Left Stick**

Теперь одно действие обрабатывает и WASD, и геймпад!

---

## Использование Input Actions в коде

### Метод 1: Компонент PlayerInput (для дизайнеров)

Самый простой подход — для базовой настройки код не нужен.

1. Добавьте компонент **PlayerInput** на ваш GameObject
2. Назначьте ассет Input Actions
3. Установите **Behavior** на "Send Messages" или "Invoke Unity Events"
4. Реализуйте методы обратного вызова:

```csharp
public class PlayerController : MonoBehaviour
{
    // Вызывается автоматически компонентом PlayerInput
    void OnMove(InputValue value)
    {
        Vector2 movement = value.Get<Vector2>();
        // Используем movement...
    }

    void OnJump(InputValue value)
    {
        // Прыжок!
    }

    void OnFire(InputValue value)
    {
        // Выстрел!
    }
}
```

### Метод 2: Генерируемый C# класс (типобезопасный)

Лучший вариант для крупных проектов. Предоставляет IntelliSense и проверку на этапе компиляции.

1. Выберите ваш ассет `.inputactions`
2. В инспекторе отметьте **Generate C# Class**
3. Нажмите **Apply**

```csharp
public class PlayerController : MonoBehaviour
{
    private MyGameControls controls; // Имя автогенерируемого класса

    void Awake()
    {
        controls = new MyGameControls();
    }

    void OnEnable()
    {
        controls.Gameplay.Enable();
        
        // Подписаться на события
        controls.Gameplay.Jump.performed += OnJump;
        controls.Gameplay.Fire.performed += OnFire;
    }

    void OnDisable()
    {
        // Всегда отписывайтесь!
        controls.Gameplay.Jump.performed -= OnJump;
        controls.Gameplay.Fire.performed -= OnFire;
        
        controls.Gameplay.Disable();
    }

    void Update()
    {
        // Опрос для непрерывного ввода
        Vector2 move = controls.Gameplay.Move.ReadValue<Vector2>();
        transform.Translate(move.x * Time.deltaTime, 0, move.y * Time.deltaTime);
    }

    void OnJump(InputAction.CallbackContext context)
    {
        Debug.Log("Прыжок!");
    }

    void OnFire(InputAction.CallbackContext context)
    {
        Debug.Log("Выстрел!");
    }
}
```

### Метод 3: InputActionReference (удобен для редактора)

Ссылка на действия прямо в инспекторе без генерации классов.

```csharp
public class PlayerController : MonoBehaviour
{
    [SerializeField] private InputActionReference moveAction;
    [SerializeField] private InputActionReference jumpAction;
    [SerializeField] private InputActionReference fireAction;

    void OnEnable()
    {
        moveAction.action.Enable();
        jumpAction.action.Enable();
        fireAction.action.Enable();
        
        jumpAction.action.performed += OnJump;
    }

    void OnDisable()
    {
        jumpAction.action.performed -= OnJump;
        
        moveAction.action.Disable();
        jumpAction.action.Disable();
        fireAction.action.Disable();
    }

    void Update()
    {
        Vector2 move = moveAction.action.ReadValue<Vector2>();
        // Используем move...
    }

    void OnJump(InputAction.CallbackContext context)
    {
        Debug.Log("Прыжок!");
    }
}
```

---

## Коллбэки против опроса

### На основе коллбэков (событийная модель)

Лучше для: дискретных действий — прыжок, выстрел, взаимодействие.

```csharp
void OnEnable()
{
    jumpAction.performed += OnJump;
    jumpAction.started += OnJumpStart;    // Кнопка нажата
    jumpAction.canceled += OnJumpCancel;  // Кнопка отпущена
}

void OnJump(InputAction.CallbackContext context)
{
    // Срабатывает один раз при выполнении действия
    Jump();
}

void OnJumpStart(InputAction.CallbackContext context)
{
    // Кнопка только что нажата — начать зарядку прыжка
    StartCharging();
}

void OnJumpCancel(InputAction.CallbackContext context)
{
    // Кнопка отпущена — выполнить заряженный прыжок
    ReleaseCharge();
}
```

### На основе опроса (проверка каждый кадр)

Лучше для: непрерывного ввода — движение, поворот камеры.

```csharp
void Update()
{
    // Считать текущее значение
    Vector2 move = moveAction.ReadValue<Vector2>();
    
    // Проверить, сработало ли действие в этом кадре
    if (jumpAction.triggered)
    {
        Jump();
    }
    
    // Проверить конкретные состояния кадра
    if (fireAction.WasPressedThisFrame())
    {
        StartFiring();
    }
    
    if (fireAction.WasReleasedThisFrame())
    {
        StopFiring();
    }
}
```

### Update против FixedUpdate

```csharp
private bool jumpRequested;

void Update()
{
    // Считывать ввод в Update для отзывчивости
    if (jumpAction.WasPressedThisFrame())
    {
        jumpRequested = true;
    }
    
    // Поворот камеры — выполнять в Update
    Vector2 look = lookAction.ReadValue<Vector2>();
    transform.Rotate(0, look.x * sensitivity * Time.deltaTime, 0);
}

void FixedUpdate()
{
    // Применять физику в FixedUpdate
    Vector2 move = moveAction.ReadValue<Vector2>();
    rb.AddForce(new Vector3(move.x, 0, move.y) * moveForce);
    
    if (jumpRequested)
    {
        rb.AddForce(Vector3.up * jumpForce, ForceMode.Impulse);
        jumpRequested = false;
    }
}
```

---

## Практические примеры

### Полное движение игрока

```csharp
using UnityEngine;
using UnityEngine.InputSystem;

public class PlayerMovement : MonoBehaviour
{
    [Header("Ссылки")]
    [SerializeField] private InputActionReference moveAction;
    [SerializeField] private InputActionReference jumpAction;
    [SerializeField] private InputActionReference sprintAction;
    
    [Header("Настройки")]
    [SerializeField] private float walkSpeed = 5f;
    [SerializeField] private float sprintSpeed = 10f;
    [SerializeField] private float jumpForce = 5f;
    
    private Rigidbody rb;
    private bool isGrounded;
    private bool jumpRequested;

    void Awake()
    {
        rb = GetComponent<Rigidbody>();
    }

    void OnEnable()
    {
        moveAction.action.Enable();
        jumpAction.action.Enable();
        sprintAction.action.Enable();
        
        jumpAction.action.performed += OnJump;
    }

    void OnDisable()
    {
        jumpAction.action.performed -= OnJump;
        
        moveAction.action.Disable();
        jumpAction.action.Disable();
        sprintAction.action.Disable();
    }

    void OnJump(InputAction.CallbackContext context)
    {
        if (isGrounded)
        {
            jumpRequested = true;
        }
    }

    void FixedUpdate()
    {
        // Движение
        Vector2 input = moveAction.action.ReadValue<Vector2>();
        float speed = sprintAction.action.IsPressed() ? sprintSpeed : walkSpeed;
        
        Vector3 move = new Vector3(input.x, 0, input.y) * speed;
        rb.velocity = new Vector3(move.x, rb.velocity.y, move.z);
        
        // Прыжок
        if (jumpRequested)
        {
            rb.AddForce(Vector3.up * jumpForce, ForceMode.Impulse);
            jumpRequested = false;
        }
    }

    void OnCollisionStay(Collision collision)
    {
        isGrounded = true;
    }

    void OnCollisionExit(Collision collision)
    {
        isGrounded = false;
    }
}
```

### Управление камерой мышью (вид от первого/третьего лица)

```csharp
using UnityEngine;
using UnityEngine.InputSystem;

public class MouseLook : MonoBehaviour
{
    [SerializeField] private InputActionReference lookAction;
    [SerializeField] private float sensitivity = 0.1f;
    [SerializeField] private float maxPitch = 85f;
    
    [SerializeField] private Transform playerBody; // Для горизонтального поворота
    
    private float pitch = 0f;

    void OnEnable()
    {
        lookAction.action.Enable();
        Cursor.lockState = CursorLockMode.Locked;
    }

    void OnDisable()
    {
        lookAction.action.Disable();
        Cursor.lockState = CursorLockMode.None;
    }

    void Update()
    {
        Vector2 look = lookAction.action.ReadValue<Vector2>();
        
        // Горизонтальный поворот (вращаем тело игрока)
        playerBody.Rotate(Vector3.up * look.x * sensitivity);
        
        // Вертикальный поворот (вращаем камеру)
        pitch -= look.y * sensitivity;
        pitch = Mathf.Clamp(pitch, -maxPitch, maxPitch);
        transform.localRotation = Quaternion.Euler(pitch, 0, 0);
    }
}
```

### Удержание и отпускание (заряженная атака)

```csharp
using UnityEngine;
using UnityEngine.InputSystem;

public class ChargeAttack : MonoBehaviour
{
    [SerializeField] private InputActionReference chargeAction;
    [SerializeField] private float maxChargeTime = 2f;
    
    private float chargeTime;
    private bool isCharging;

    void OnEnable()
    {
        chargeAction.action.Enable();
        chargeAction.action.started += OnChargeStart;
        chargeAction.action.canceled += OnChargeRelease;
    }

    void OnDisable()
    {
        chargeAction.action.started -= OnChargeStart;
        chargeAction.action.canceled -= OnChargeRelease;
        chargeAction.action.Disable();
    }

    void OnChargeStart(InputAction.CallbackContext context)
    {
        isCharging = true;
        chargeTime = 0f;
    }

    void OnChargeRelease(InputAction.CallbackContext context)
    {
        if (isCharging)
        {
            float chargePercent = Mathf.Clamp01(chargeTime / maxChargeTime);
            Fire(chargePercent);
            isCharging = false;
        }
    }

    void Update()
    {
        if (isCharging)
        {
            chargeTime += Time.deltaTime;
            // Показать UI зарядки...
        }
    }

    void Fire(float power)
    {
        Debug.Log($"Выстрел с мощностью {power * 100}%!");
    }
}
```

---

## Переназначение клавиш во время выполнения

Позвольте игрокам настраивать управление в процессе игры.

### Отображение текущей привязки

```csharp
using UnityEngine;
using UnityEngine.InputSystem;
using TMPro;

public class BindingDisplay : MonoBehaviour
{
    [SerializeField] private InputActionReference action;
    [SerializeField] private TMP_Text bindingText;
    [SerializeField] private int bindingIndex = 0; // 0 для первой привязки

    void Start()
    {
        UpdateDisplay();
    }

    void UpdateDisplay()
    {
        bindingText.text = action.action.GetBindingDisplayString(bindingIndex);
    }
}
```

### Интерактивное переназначение

```csharp
using UnityEngine;
using UnityEngine.InputSystem;
using UnityEngine.UI;
using TMPro;

public class RebindUI : MonoBehaviour
{
    [SerializeField] private InputActionReference actionToRebind;
    [SerializeField] private int bindingIndex = 0;
    [SerializeField] private Button rebindButton;
    [SerializeField] private TMP_Text bindingText;
    [SerializeField] private GameObject waitingOverlay;

    private InputActionRebindingExtensions.RebindingOperation rebindOperation;

    void Start()
    {
        rebindButton.onClick.AddListener(StartRebinding);
        UpdateBindingDisplay();
    }

    void StartRebinding()
    {
        // Отключить действие на время переназначения
        actionToRebind.action.Disable();
        
        rebindButton.interactable = false;
        waitingOverlay.SetActive(true);

        rebindOperation = actionToRebind.action.PerformInteractiveRebinding(bindingIndex)
            .WithControlsExcluding("<Mouse>/position")
            .WithControlsExcluding("<Mouse>/delta")
            .WithCancelingThrough("<Keyboard>/escape")
            .OnComplete(operation => RebindComplete())
            .OnCancel(operation => RebindCanceled())
            .Start();
    }

    void RebindComplete()
    {
        rebindOperation.Dispose();
        rebindOperation = null;

        rebindButton.interactable = true;
        waitingOverlay.SetActive(false);
        
        actionToRebind.action.Enable();
        UpdateBindingDisplay();
    }

    void RebindCanceled()
    {
        rebindOperation.Dispose();
        rebindOperation = null;

        rebindButton.interactable = true;
        waitingOverlay.SetActive(false);
        
        actionToRebind.action.Enable();
    }

    void UpdateBindingDisplay()
    {
        bindingText.text = actionToRebind.action.GetBindingDisplayString(bindingIndex);
    }

    void OnDestroy()
    {
        rebindOperation?.Dispose();
    }
}
```

### Сохранение и загрузка привязок

```csharp
using UnityEngine;
using UnityEngine.InputSystem;

public class BindingSaveLoad : MonoBehaviour
{
    [SerializeField] private InputActionAsset inputActions;
    
    private const string BINDINGS_KEY = "InputBindings";

    public void SaveBindings()
    {
        string json = inputActions.SaveBindingOverridesAsJson();
        PlayerPrefs.SetString(BINDINGS_KEY, json);
        PlayerPrefs.Save();
    }

    public void LoadBindings()
    {
        if (PlayerPrefs.HasKey(BINDINGS_KEY))
        {
            string json = PlayerPrefs.GetString(BINDINGS_KEY);
            inputActions.LoadBindingOverridesFromJson(json);
        }
    }

    public void ResetToDefaults()
    {
        inputActions.RemoveAllBindingOverrides();
        PlayerPrefs.DeleteKey(BINDINGS_KEY);
    }

    void Awake()
    {
        LoadBindings();
    }
}
```

---

## Лучшие практики

### 1. Всегда включайте/отключайте действия
```csharp
void OnEnable()
{
    myAction.action.Enable();
}

void OnDisable()
{
    myAction.action.Disable();
}
```

### 2. Всегда отписывайтесь от коллбэков
```csharp
void OnEnable()
{
    jumpAction.performed += OnJump;
}

void OnDisable()
{
    jumpAction.performed -= OnJump; // Предотвращает утечки памяти!
}
```

### 3. Проверяйте доступность устройства
```csharp
void Update()
{
    if (Gamepad.current == null) return; // Геймпад не подключён
    
    Vector2 stick = Gamepad.current.leftStick.ReadValue();
}
```

### 4. Используйте Action Maps для состояний игры
```csharp
void EnterMenu()
{
    controls.Gameplay.Disable();
    controls.UI.Enable();
}

void ExitMenu()
{
    controls.UI.Disable();
    controls.Gameplay.Enable();
}
```

### 5. Кэшируйте значения InputActionReference
```csharp
// Правильно — кэшировать действие
private InputAction cachedMoveAction;

void Awake()
{
    cachedMoveAction = moveActionRef.action;
}

void Update()
{
    Vector2 move = cachedMoveAction.ReadValue<Vector2>();
}
```

---

## Краткий справочник

### Доступ к устройствам
```csharp
Keyboard.current      // Текущая клавиатура
Mouse.current         // Текущая мышь
Gamepad.current       // Первый подключённый геймпад
Gamepad.all[0]        // Конкретный геймпад по индексу
Touchscreen.current   // Текущий сенсорный экран
```

### Состояния кнопок
```csharp
button.isPressed              // Удерживается прямо сейчас
button.wasPressedThisFrame    // Только что нажата в этом кадре
button.wasReleasedThisFrame   // Только что отпущена в этом кадре
```

### Состояния действий
```csharp
action.triggered              // Выполнено в этом кадре
action.WasPressedThisFrame()  // Началось в этом кадре
action.WasReleasedThisFrame() // Отменено в этом кадре
action.ReadValue<T>()         // Текущее значение
action.IsPressed()            // Активно в данный момент
```

### Иерархия включения/отключения
```csharp
action.Enable() / action.Disable()        // Одно действие
actionMap.Enable() / actionMap.Disable()  // Карта действий
asset.Enable() / asset.Disable()          // Весь ассет
```

### Коллбэки
```csharp
action.started += ctx => { };   // Ввод начался
action.performed += ctx => { }; // Ввод выполнен (основное событие)
action.canceled += ctx => { };  // Ввод завершён
```

---

## Частые ошибки

1. **Действия должны быть включены**: действия не работают, пока вы не вызовете `.Enable()`
2. **Отключайте в OnDisable**: предотвращает лишние события ввода и утечки памяти
3. **Отписывайтесь от коллбэков**: всегда отписывайтесь в `OnDisable` или `OnDestroy`
4. **Проверка устройства на null**: `Gamepad.current` равен null, если геймпад не подключён
5. **Имена генерируемых классов**: генерируемый класс использует имя вашего файла `.inputactions`
6. **Индекс привязки**: при переназначении используйте правильный индекс (0 = первая привязка)
7. **Физика и ввод**: считывайте ввод в `Update`, применяйте физику в `FixedUpdate`
