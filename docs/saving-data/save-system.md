# SaveSystem — универсальная система сохранений

`SaveSystem` — сервисный класс, объединяющий JSON-сериализацию и файловую систему в удобный API. Здесь также разобраны шифрование данных и автосохранение.

## Базовый SaveSystem

```csharp
using System;
using System.IO;
using UnityEngine;

public static class SaveSystem
{
    static string GetPath(string fileName) =>
        Path.Combine(Application.persistentDataPath, fileName + ".json");

    // ─── Сохранение ───────────────────────────────────────────────
    public static void Save<T>(string fileName, T data)
    {
        string path = GetPath(fileName);
        string json = JsonUtility.ToJson(data, prettyPrint: true);

        try
        {
            // Атомарная запись через временный файл
            string temp = path + ".tmp";
            File.WriteAllText(temp, json);
            if (File.Exists(path)) File.Delete(path);
            File.Move(temp, path);
        }
        catch (Exception e)
        {
            Debug.LogError($"[SaveSystem] Ошибка записи '{fileName}': {e.Message}");
        }
    }

    // ─── Загрузка ─────────────────────────────────────────────────
    public static T Load<T>(string fileName) where T : new()
    {
        string path = GetPath(fileName);

        if (!File.Exists(path))
        {
            Debug.Log($"[SaveSystem] Файл '{fileName}' не найден, возвращаем значения по умолчанию.");
            return new T();
        }

        try
        {
            string json = File.ReadAllText(path);
            return JsonUtility.FromJson<T>(json);
        }
        catch (Exception e)
        {
            Debug.LogError($"[SaveSystem] Ошибка чтения '{fileName}': {e.Message}");
            return new T();
        }
    }

    // ─── Служебные ────────────────────────────────────────────────
    public static bool Exists(string fileName) => File.Exists(GetPath(fileName));

    public static void Delete(string fileName)
    {
        string path = GetPath(fileName);
        if (File.Exists(path)) File.Delete(path);
    }
}
```

### Использование

```csharp
[Serializable]
public class PlayerData
{
    public string name   = "Игрок";
    public int    level  = 1;
    public float  health = 100f;
    public Vector3 position;
}

// Сохранить
PlayerData data = new PlayerData { name = "Иван", level = 5, health = 80f };
SaveSystem.Save("player", data);

// Загрузить
PlayerData loaded = SaveSystem.Load<PlayerData>("player");
Debug.Log(loaded.level); // 5

// Проверить / удалить
if (SaveSystem.Exists("player"))
    SaveSystem.Delete("player");
```

## Несколько слотов сохранения

```csharp
public static class SaveSlots
{
    const int MaxSlots = 3;

    public static void Save(int slot, PlayerData data)
    {
        if (slot < 0 || slot >= MaxSlots) return;
        SaveSystem.Save($"save_slot_{slot}", data);
    }

    public static PlayerData Load(int slot) =>
        SaveSystem.Load<PlayerData>($"save_slot_{slot}");

    public static bool Exists(int slot) =>
        SaveSystem.Exists($"save_slot_{slot}");

    public static void Delete(int slot) =>
        SaveSystem.Delete($"save_slot_{slot}");

    /// Заголовки всех слотов (для UI выбора сохранения)
    public static PlayerData[] GetAllHeaders()
    {
        var result = new PlayerData[MaxSlots];
        for (int i = 0; i < MaxSlots; i++)
            result[i] = Exists(i) ? Load(i) : null;
        return result;
    }
}
```

## Шифрование данных (Base64 + XOR)

Лёгкая защита от случайного редактирования файла пользователем. **Не является криптографически стойкой защитой.**

```csharp
public static class SaveSystem
{
    const string EncryptKey = "MySecretKey123"; // поменяйте на свой ключ

    static string Encrypt(string data)
    {
        byte[] bytes  = System.Text.Encoding.UTF8.GetBytes(data);
        byte[] keyBytes = System.Text.Encoding.UTF8.GetBytes(EncryptKey);
        for (int i = 0; i < bytes.Length; i++)
            bytes[i] ^= keyBytes[i % keyBytes.Length];
        return Convert.ToBase64String(bytes);
    }

    static string Decrypt(string data)
    {
        byte[] bytes    = Convert.FromBase64String(data);
        byte[] keyBytes = System.Text.Encoding.UTF8.GetBytes(EncryptKey);
        for (int i = 0; i < bytes.Length; i++)
            bytes[i] ^= keyBytes[i % keyBytes.Length];
        return System.Text.Encoding.UTF8.GetString(bytes);
    }

    public static void SaveEncrypted<T>(string fileName, T data)
    {
        string json      = JsonUtility.ToJson(data);
        string encrypted = Encrypt(json);
        File.WriteAllText(GetPath(fileName), encrypted);
    }

    public static T LoadEncrypted<T>(string fileName) where T : new()
    {
        string path = GetPath(fileName);
        if (!File.Exists(path)) return new T();
        try
        {
            string encrypted = File.ReadAllText(path);
            string json      = Decrypt(encrypted);
            return JsonUtility.FromJson<T>(json);
        }
        catch { return new T(); }
    }

    static string GetPath(string fileName) =>
        Path.Combine(Application.persistentDataPath, fileName + ".sav");
}
```

## Автосохранение

```csharp
public class AutoSave : MonoBehaviour
{
    [SerializeField] float intervalSeconds = 60f;

    void Start() => StartCoroutine(AutoSaveRoutine());

    IEnumerator AutoSaveRoutine()
    {
        var wait = new WaitForSeconds(intervalSeconds);
        while (true)
        {
            yield return wait;
            DoSave();
            Debug.Log($"[AutoSave] Сохранено в {System.DateTime.Now:HH:mm:ss}");
        }
    }

    void DoSave()
    {
        // Собрать актуальные данные и сохранить
        var data = GameManager.Instance.CollectSaveData();
        SaveSystem.Save("autosave", data);
    }

    // Сохранить при закрытии приложения
    void OnApplicationPause(bool pause) { if (pause) DoSave(); }
    void OnApplicationQuit() => DoSave();
}
```

## Асинхронное сохранение (не блокирует UI)

```csharp
using System.Threading.Tasks;

public static async Task SaveAsync<T>(string fileName, T data)
{
    string json = JsonUtility.ToJson(data, prettyPrint: true);
    string path = GetPath(fileName);

    try
    {
        await System.IO.File.WriteAllTextAsync(path, json);
    }
    catch (Exception e)
    {
        Debug.LogError($"[SaveSystem] Async error: {e.Message}");
    }
}

// Использование
await SaveSystem.SaveAsync("player", playerData);
```

## Рекомендации

- Всегда используйте **атомарную запись** (через .tmp-файл) для основных сохранений — защита от повреждения при крэше.
- Разделяйте **настройки** (PlayerPrefs) и **игровое состояние** (SaveSystem/JSON) — у них разные требования к надёжности.
- Вызывайте `DoSave()` в `OnApplicationPause` и `OnApplicationQuit` — мобильные приложения часто закрываются без предупреждения.
- Для мультиплеера и облачных сохранений рассмотрите **Unity Gaming Services → Cloud Save**.
- Не сохраняйте ссылки на `MonoBehaviour` — только данные (числа, строки, структуры).
