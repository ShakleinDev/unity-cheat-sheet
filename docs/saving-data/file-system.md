# Файловая система — чтение и запись файлов

`System.IO` позволяет читать и записывать файлы напрямую. Используется вместе с JSON-сериализацией для сохранения прогресса игрока.

## Пути сохранения

```csharp
using System.IO;
using UnityEngine;

// Application.persistentDataPath — рекомендуемый путь для сохранений
// Windows:  C:/Users/<user>/AppData/LocalLow/<Company>/<Product>/
// macOS:    ~/Library/Application Support/<Company>/<Product>/
// Android:  /data/data/<package>/files/
// iOS:      <app>/Documents/

string savePath = Path.Combine(Application.persistentDataPath, "save.json");
Debug.Log(savePath);
```

## Запись текстового файла

```csharp
// Перезаписать или создать файл
File.WriteAllText(savePath, jsonString);

// Добавить строку в конец (лог, история)
File.AppendAllText(logPath, $"[{System.DateTime.Now}] {message}\n");
```

## Чтение текстового файла

```csharp
if (File.Exists(savePath))
{
    string json = File.ReadAllText(savePath);
    // ... десериализация
}
else
{
    Debug.Log("Файл сохранения не найден, создаём новый.");
}
```

## Запись и чтение байтов (бинарные данные)

```csharp
// Запись
byte[] bytes = System.Text.Encoding.UTF8.GetBytes(jsonString);
File.WriteAllBytes(savePath, bytes);

// Чтение
byte[] loaded = File.ReadAllBytes(savePath);
string json   = System.Text.Encoding.UTF8.GetString(loaded);
```

## Управление файлами и директориями

```csharp
// Проверить существование
File.Exists(savePath);
Directory.Exists(dirPath);

// Создать директорию (и все промежуточные)
Directory.CreateDirectory(dirPath);

// Удалить файл
File.Delete(savePath);

// Список файлов в директории
string[] files = Directory.GetFiles(Application.persistentDataPath, "*.json");

// Копировать файл
File.Copy(sourcePath, destPath, overwrite: true);
```

## Несколько слотов сохранения

```csharp
string GetSlotPath(int slot) =>
    Path.Combine(Application.persistentDataPath, $"save_slot_{slot}.json");

void SaveToSlot(int slot, PlayerData data)
{
    string path = GetSlotPath(slot);
    File.WriteAllText(path, JsonUtility.ToJson(data, prettyPrint: true));
}

PlayerData LoadFromSlot(int slot)
{
    string path = GetSlotPath(slot);
    if (!File.Exists(path)) return null;
    return JsonUtility.FromJson<PlayerData>(File.ReadAllText(path));
}

bool SlotExists(int slot) => File.Exists(GetSlotPath(slot));

void DeleteSlot(int slot)
{
    string path = GetSlotPath(slot);
    if (File.Exists(path)) File.Delete(path);
}
```

## Атомарная запись (защита от повреждения)

```csharp
// Записываем во временный файл, потом переименовываем —
// если запись прервётся, основной файл останется нетронутым.
void SafeWrite(string path, string content)
{
    string tempPath = path + ".tmp";
    File.WriteAllText(tempPath, content);
    File.Delete(path);              // удалить старый
    File.Move(tempPath, path);      // переименовать .tmp → основной
}
```

## Рекомендации

- Всегда используйте `Application.persistentDataPath` — другие пути (`dataPath`, `streamingAssetsPath`) могут быть недоступны для записи на некоторых платформах.
- Оборачивайте файловые операции в `try-catch IOException` — диск может быть заполнен или файл заблокирован.
- Для важных сохранений используйте **атомарную запись** через временный файл.
- На мобильных платформах `File.IO` работает в главном потоке — для больших файлов используйте `async/await`, чтобы не подвисал UI.
