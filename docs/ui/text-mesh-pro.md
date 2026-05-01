# TextMeshPro — текст в Unity

TextMeshPro (TMP) — рекомендуемый способ работы с текстом в Unity. Обеспечивает чёткий рендеринг при любом масштабе (SDF-шрифты), богатое форматирование и высокую производительность.

## Установка и импорт

```csharp
using TMPro; // подключить пространство имён

// Два компонента:
// TextMeshProUGUI — для UI (Canvas)
// TextMeshPro     — для мирового пространства (3D)
```

## Получение и изменение текста

```csharp
TextMeshProUGUI label = GetComponent<TextMeshProUGUI>();

// Установить текст
label.text = "Счёт: 42";

// Прочитать текст
string current = label.text;

// Строковая интерполяция
int score = 100;
label.text = $"Очки: {score}";
```

## Шрифт, размер, цвет

```csharp
TextMeshProUGUI label = GetComponent<TextMeshProUGUI>();

// Размер шрифта
label.fontSize = 36f;

// Автоподбор размера
label.enableAutoSizing = true;
label.fontSizeMin = 12f;
label.fontSizeMax = 48f;

// Цвет
label.color = Color.white;
label.color = new Color(1f, 0.5f, 0f, 1f); // RGBA

// Назначить шрифт из кода
label.font = Resources.Load<TMP_FontAsset>("Fonts/Roboto");
```

## Выравнивание

```csharp
using TMPro;

label.alignment = TextAlignmentOptions.Center;
label.alignment = TextAlignmentOptions.TopLeft;
label.alignment = TextAlignmentOptions.BottomRight;
label.alignment = TextAlignmentOptions.MidlineJustified;
```

## Rich Text — встроенное форматирование

```csharp
// TMP поддерживает теги прямо в строке
label.text = "<b>Жирный</b> и <i>курсив</i>";
label.text = "<color=red>Красный</color> текст";
label.text = "<color=#FF6600>Оранжевый</color>";
label.text = "<size=48>Большой</size> и <size=24>маленький</size>";
label.text = "<u>Подчёркнутый</u> и <s>зачёркнутый</s>";
label.text = "Иконка: <sprite=0>"; // вставить спрайт из TMP Sprite Asset
label.text = "<link=\"url\">Кликабельный текст</link>";
```

## Динамическое построение строк

```csharp
// Избегать конкатенации в Update — генерирует мусор
// Плохо:
label.text = "HP: " + hp + "/" + maxHp;

// Хорошо — StringBuilder
using System.Text;
StringBuilder sb = new StringBuilder();
sb.Clear();
sb.Append("HP: ");
sb.Append(hp);
sb.Append('/');
sb.Append(maxHp);
label.text = sb.ToString();
```

## Получение размеров текста

```csharp
// Принудительно пересчитать геометрию
label.ForceMeshUpdate();

// Размер отрендеренного текста
Vector2 size = label.GetRenderedValues(onlyVisibleCharacters: true);
Debug.Log($"Ширина: {size.x}, Высота: {size.y}");

// Количество символов
int count = label.textInfo.characterCount;
```

## Анимация отдельных символов

```csharp
IEnumerator AnimateChars(TextMeshProUGUI tmp)
{
    tmp.ForceMeshUpdate();
    TMP_TextInfo info = tmp.textInfo;

    for (int i = 0; i < info.characterCount; i++)
    {
        TMP_CharacterInfo ch = info.characterInfo[i];
        if (!ch.isVisible) continue;

        int meshIndex = ch.materialReferenceIndex;
        int vertIndex = ch.vertexIndex;

        Vector3[] verts = info.meshInfo[meshIndex].vertices;
        for (int j = 0; j < 4; j++)
            verts[vertIndex + j] += Vector3.up * 5f;

        tmp.UpdateVertexData(TMP_VertexDataUpdateFlags.Vertices);
        yield return new WaitForSeconds(0.05f);
    }
}
```

## TextMeshPro в 3D (мировое пространство)

```csharp
// Компонент TextMeshPro (не UGUI) — крепится к обычному GameObject
TextMeshPro tmp3D = GetComponent<TextMeshPro>();
tmp3D.text = "Надпись в мире";
tmp3D.fontSize = 5f;
tmp3D.alignment = TextAlignmentOptions.Center;
```

## Рекомендации

- Всегда используйте TMP вместо устаревшего `Text` из `UnityEngine.UI`.
- Создавайте атласы шрифтов через **Window → TextMeshPro → Font Asset Creator**.
- Для многоязычного текста добавляйте нужные диапазоны символов в Font Asset.
- Не меняйте `label.text` каждый кадр без необходимости — это дорого. Обновляйте только при изменении значения.
- Используйте **TMP Sprite Asset** для вставки иконок (иконки предметов, кнопок геймпада) прямо в текст.
