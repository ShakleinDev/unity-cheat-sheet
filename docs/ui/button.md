# UI Button

## Базовое использование

```csharp
using UnityEngine.UI;

Button btn = GetComponent<Button>();

// Подписаться на клик
btn.onClick.AddListener(OnButtonClick);

void OnButtonClick()
{
    Debug.Log("Кнопка нажата!");
}

// Отписаться
void OnDestroy()
{
    btn.onClick.RemoveListener(OnButtonClick);
}
```

## Блокировка и разблокировка

```csharp
// Запретить нажатие (кнопка становится серой, если настроены цвета)
btn.interactable = false;

// Разблокировать
btn.interactable = true;
```

## Программный клик

```csharp
// Имитировать нажатие из кода
btn.onClick.Invoke();
```

## Цвета состояний

```csharp
ColorBlock colors = btn.colors;

colors.normalColor      = Color.white;
colors.highlightedColor = new Color(0.9f, 0.9f, 0.9f);
colors.pressedColor     = new Color(0.7f, 0.7f, 0.7f);
colors.disabledColor    = new Color(0.5f, 0.5f, 0.5f, 0.5f);
colors.fadeDuration     = 0.1f;

btn.colors = colors;
```

## Смена текста и иконки кнопки

```csharp
using TMPro;

// Текст на кнопке (дочерний TextMeshProUGUI)
TextMeshProUGUI label = btn.GetComponentInChildren<TextMeshProUGUI>();
label.text = "Начать игру";

// Иконка (дочерний Image)
Image icon = btn.transform.Find("Icon").GetComponent<Image>();
icon.sprite = newSprite;
```

## Lambda-подписка с параметром

```csharp
int itemId = 42;
btn.onClick.AddListener(() => OnItemSelected(itemId));

void OnItemSelected(int id)
{
    Debug.Log($"Выбран предмет: {id}");
}
```

## Создание кнопок в рантайме

```csharp
[SerializeField] Button buttonPrefab;
[SerializeField] Transform container;

void CreateButtons(string[] labels)
{
    foreach (string text in labels)
    {
        Button b = Instantiate(buttonPrefab, container);
        b.GetComponentInChildren<TextMeshProUGUI>().text = text;

        string captured = text; // захватить переменную для замыкания
        b.onClick.AddListener(() => Debug.Log($"Нажато: {captured}"));
    }
}
```

## Рекомендации

- Всегда отписывайтесь от `onClick` в `OnDestroy` при динамических кнопках.
- В цикле **захватывайте переменную** в локальную переменную перед лямбдой — иначе все кнопки будут ссылаться на одно значение.
- Используйте `btn.interactable = false` вместо `gameObject.SetActive(false)` там, где кнопка должна оставаться видимой, но неактивной.
