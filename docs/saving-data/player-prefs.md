# PlayerPrefs — простое хранилище данных

`PlayerPrefs` хранит данные в системном реестре (Windows) или в файле настроек (macOS, Linux, Android, iOS). Подходит для простых настроек: громкость, язык, лучший результат.

## Запись и чтение

```csharp
// --- int ---
PlayerPrefs.SetInt("Score", 1500);
int score = PlayerPrefs.GetInt("Score");          // 0, если ключ не найден
int score = PlayerPrefs.GetInt("Score", 0);       // явно задать значение по умолчанию

// --- float ---
PlayerPrefs.SetFloat("Volume", 0.8f);
float volume = PlayerPrefs.GetFloat("Volume", 1f);

// --- string ---
PlayerPrefs.SetString("PlayerName", "Иван");
string name = PlayerPrefs.GetString("PlayerName", "Гость");
```

## Проверка наличия ключа и удаление

```csharp
// Проверить, существует ли ключ
if (PlayerPrefs.HasKey("Score"))
{
    int score = PlayerPrefs.GetInt("Score");
}

// Удалить один ключ
PlayerPrefs.DeleteKey("Score");

// Удалить все сохранённые значения
PlayerPrefs.DeleteAll();
```

## Принудительная запись на диск

```csharp
// По умолчанию PlayerPrefs сохраняется при закрытии приложения.
// Вызовите Save() явно, чтобы записать немедленно (например, после покупки).
PlayerPrefs.Save();
```

## Типичный паттерн: класс настроек

```csharp
public static class GameSettings
{
    const string KeyVolume   = "Settings_Volume";
    const string KeyLanguage = "Settings_Language";
    const string KeyBestScore = "BestScore";

    public static float Volume
    {
        get => PlayerPrefs.GetFloat(KeyVolume, 1f);
        set { PlayerPrefs.SetFloat(KeyVolume, value); PlayerPrefs.Save(); }
    }

    public static string Language
    {
        get => PlayerPrefs.GetString(KeyLanguage, "ru");
        set { PlayerPrefs.SetString(KeyLanguage, value); PlayerPrefs.Save(); }
    }

    public static int BestScore
    {
        get => PlayerPrefs.GetInt(KeyBestScore, 0);
        set
        {
            if (value > BestScore)
            {
                PlayerPrefs.SetInt(KeyBestScore, value);
                PlayerPrefs.Save();
            }
        }
    }
}
```

```csharp
// Использование
GameSettings.Volume = 0.5f;
Debug.Log(GameSettings.BestScore);
```

## Хранение bool через int

```csharp
// PlayerPrefs не поддерживает bool напрямую — используйте int (0/1)
PlayerPrefs.SetInt("TutorialDone", 1);
bool done = PlayerPrefs.GetInt("TutorialDone", 0) == 1;
```

## Рекомендации

- **Не храните** чувствительные данные (пароли, покупки) в PlayerPrefs — они хранятся в открытом виде.
- Используйте **константы** для ключей, чтобы избежать опечаток.
- PlayerPrefs подходит для **простых настроек** (звук, язык, лучший счёт). Для сложного состояния игры используйте JSON-сериализацию или `SaveSystem`.
- Вызывайте `Save()` явно после критически важных записей — иначе данные могут потеряться при крэше.
