# InputField (TextMeshPro)

`TMP_InputField` — рекомендуемый компонент для текстового ввода. Поддерживает валидацию, плейсхолдер, многострочный режим и события.

## Базовое использование

```csharp
using TMPro;

TMP_InputField inputField = GetComponent<TMP_InputField>();

// Получить введённый текст
string value = inputField.text;

// Установить текст программно
inputField.text = "Иван";

// Очистить поле
inputField.text = string.Empty;

// Поставить фокус (открыть клавиатуру)
inputField.ActivateInputField();

// Снять фокус
inputField.DeactivateInputField();
```

## Плейсхолдер

```csharp
// Плейсхолдер — дочерний объект InputField с компонентом TextMeshProUGUI
// Управляется автоматически, но можно изменить текст:
TMP_Text placeholder = inputField.placeholder as TMP_Text;
if (placeholder != null)
    placeholder.text = "Введите имя...";
```

## События

```csharp
// Вызывается при каждом изменении текста
inputField.onValueChanged.AddListener(OnTextChanged);

void OnTextChanged(string value)
{
    Debug.Log($"Текущий ввод: {value}");
}

// Вызывается при завершении ввода (Enter или потеря фокуса)
inputField.onEndEdit.AddListener(OnEndEdit);

void OnEndEdit(string value)
{
    Debug.Log($"Подтверждено: {value}");
}

// Вызывается только по нажатию Enter (не при потере фокуса)
inputField.onSubmit.AddListener(OnSubmit);

void OnSubmit(string value)
{
    Debug.Log($"Submit: {value}");
}

// Отписка
void OnDestroy()
{
    inputField.onValueChanged.RemoveListener(OnTextChanged);
    inputField.onEndEdit.RemoveListener(OnEndEdit);
}
```

## Типы контента и валидация

```csharp
// Разрешить только определённый тип символов
inputField.contentType = TMP_InputField.ContentType.Standard;       // любой текст
inputField.contentType = TMP_InputField.ContentType.IntegerNumber;  // целые числа
inputField.contentType = TMP_InputField.ContentType.DecimalNumber;  // числа с точкой
inputField.contentType = TMP_InputField.ContentType.EmailAddress;   // e-mail
inputField.contentType = TMP_InputField.ContentType.Password;       // скрытые символы
inputField.contentType = TMP_InputField.ContentType.Pin;            // цифры (PIN)
inputField.contentType = TMP_InputField.ContentType.Alphanumeric;   // буквы и цифры

// Ограничение длины (0 = без ограничений)
inputField.characterLimit = 20;
```

## Многострочный режим

```csharp
inputField.lineType = TMP_InputField.LineType.MultiLineNewline;  // Enter = новая строка
inputField.lineType = TMP_InputField.LineType.MultiLineSubmit;   // Enter = Submit
inputField.lineType = TMP_InputField.LineType.SingleLine;        // одна строка (по умолчанию)

// Максимум строк
inputField.lineLimit = 5; // 0 = без ограничений
```

## Пример: поле имени игрока с валидацией

```csharp
[SerializeField] TMP_InputField nameField;
[SerializeField] Button confirmButton;
[SerializeField] TextMeshProUGUI errorLabel;

void Start()
{
    confirmButton.interactable = false;
    nameField.onValueChanged.AddListener(ValidateName);
}

void ValidateName(string value)
{
    bool valid = value.Length >= 3 && value.Length <= 16;
    confirmButton.interactable = valid;
    errorLabel.gameObject.SetActive(!valid && value.Length > 0);
    errorLabel.text = value.Length < 3 ? "Минимум 3 символа" : "Максимум 16 символов";
}

public void OnConfirm()
{
    PlayerPrefs.SetString("PlayerName", nameField.text);
    Debug.Log($"Имя сохранено: {nameField.text}");
}
```

## Рекомендации

- Используйте `TMP_InputField` вместо устаревшего `InputField` из `UnityEngine.UI`.
- Для паролей устанавливайте `contentType = Password` — символы автоматически скрываются.
- Подписывайтесь на `onEndEdit`, а не на `onValueChanged`, если вам нужен только финальный результат — меньше вызовов.
- На мобильных устройствах `ActivateInputField()` открывает системную клавиатуру.
- Отписывайтесь от событий в `OnDestroy`, чтобы избежать утечек памяти.
