# Dropdown (TextMeshPro)

`TMP_Dropdown` — выпадающий список с поддержкой TextMeshPro. Используется для выбора языка, качества графики, персонажей и любых перечислимых настроек.

## Базовое использование

```csharp
using TMPro;

TMP_Dropdown dropdown = GetComponent<TMP_Dropdown>();

// Получить индекс выбранного элемента
int index = dropdown.value;

// Получить текст выбранного элемента
string selected = dropdown.options[dropdown.value].text;

// Установить выбранный элемент по индексу
dropdown.value = 2;

// Установить без вызова события onValueChanged
dropdown.SetValueWithoutNotify(2);
```

## Заполнение списка из кода

```csharp
// Очистить и заполнить строками
dropdown.ClearOptions();

List<string> options = new List<string> { "Низкое", "Среднее", "Высокое", "Ультра" };
dropdown.AddOptions(options);

// Заполнить с иконками (Sprite)
List<TMP_Dropdown.OptionData> richOptions = new List<TMP_Dropdown.OptionData>
{
    new TMP_Dropdown.OptionData("Воин",  warriorSprite),
    new TMP_Dropdown.OptionData("Маг",   mageSprite),
    new TMP_Dropdown.OptionData("Лучник", archerSprite),
};
dropdown.ClearOptions();
dropdown.AddOptions(richOptions);
```

## Заполнение из enum

```csharp
public enum Quality { Low, Medium, High, Ultra }

void PopulateFromEnum()
{
    dropdown.ClearOptions();

    List<string> names = new List<string>(System.Enum.GetNames(typeof(Quality)));
    dropdown.AddOptions(names);

    // Восстановить сохранённое значение
    dropdown.value = PlayerPrefs.GetInt("Quality", (int)Quality.High);
}
```

## Событие изменения выбора

```csharp
dropdown.onValueChanged.AddListener(OnQualityChanged);

void OnQualityChanged(int index)
{
    Quality q = (Quality)index;
    Debug.Log($"Выбрано качество: {q}");
    PlayerPrefs.SetInt("Quality", index);
    ApplyQuality(q);
}

void OnDestroy()
{
    dropdown.onValueChanged.RemoveListener(OnQualityChanged);
}
```

## Пример: выбор языка

```csharp
[SerializeField] TMP_Dropdown languageDropdown;

readonly string[] languageCodes = { "ru", "en", "de", "zh" };

void Start()
{
    languageDropdown.ClearOptions();
    languageDropdown.AddOptions(new List<string> { "Русский", "English", "Deutsch", "中文" });

    // Установить текущий язык
    string current = PlayerPrefs.GetString("Language", "ru");
    languageDropdown.value = System.Array.IndexOf(languageCodes, current);

    languageDropdown.onValueChanged.AddListener(index =>
    {
        PlayerPrefs.SetString("Language", languageCodes[index]);
        ApplyLanguage(languageCodes[index]);
    });
}
```

## Управление состоянием

```csharp
// Заблокировать выбор
dropdown.interactable = false;

// Открыть / закрыть список программно
dropdown.Show();
dropdown.Hide();

// Очистить все варианты
dropdown.ClearOptions();

// Добавить один вариант
dropdown.options.Add(new TMP_Dropdown.OptionData("Новый"));
dropdown.RefreshShownValue(); // обновить отображаемый текст
```

## Рекомендации

- Используйте `TMP_Dropdown` вместо устаревшего `Dropdown` из `UnityEngine.UI`.
- При заполнении из enum используйте `SetValueWithoutNotify`, чтобы не вызвать событие при инициализации.
- Для сохранения выбора используйте `PlayerPrefs` или `ScriptableObject` — не храните индекс в статике.
- `dropdown.RefreshShownValue()` необходим, если вы напрямую изменяете `dropdown.options` без `AddOptions` / `ClearOptions`.
