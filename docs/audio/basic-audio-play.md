# Unity Audio - Воспроизведение звуков и музыки

## Воспроизведение

```csharp
public class PlayAudio : MonoBehaviour {
    public AudioSource audioSource;

    void Start() {
        // Вызов Play на AudioSource, который уже воспроизводится, запустит его с начала
        audioSource.Play();
    }
}
```

## Пауза и остановка

```csharp
public class PauseStopAudio : MonoBehaviour {
    public AudioSource audioSource;

    void PauseAudio() {
        // Ставит воспроизведение на паузу — при повторном вызове Play продолжится с того же места
        audioSource.Pause();
    }

    void UnPauseAudio() {
        // Снимает паузу и продолжает воспроизведение с места остановки
        audioSource.UnPause();
    }

    void StopAudio() {
        // Полностью останавливает воспроизведение — при повторном вызове Play начнётся с начала
        audioSource.Stop();
    }
}
```

## Управление громкостью

```csharp
public class VolumeControl : MonoBehaviour {
    public AudioSource audioSource;

    void Start() {
        // Громкость задаётся в диапазоне от 0.0 (тишина) до 1.0 (максимум)
        audioSource.volume = 0.5f;

        // Воспроизвести клип один раз с заданной громкостью (не изменяет volume самого AudioSource)
        audioSource.PlayOneShot(audioSource.clip, 0.8f);
    }

    void SetVolume(float value) {
        // Установить громкость динамически, например из слайдера UI
        audioSource.volume = Mathf.Clamp01(value);
    }
}
```

