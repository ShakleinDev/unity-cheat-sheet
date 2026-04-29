# Unity Fade UI Element - Анимация прозрачности CanvasGroup

Плавно показывайте или скрывайте подсказки в мировом пространстве, HUD-панели или тултипы, интерполируя альфу `CanvasGroup`. Корутины или твины управляют временем без необходимости создавать полноценный анимационный клип.

## Поведение

1. Закэшировать компонент `CanvasGroup` (добавить, если отсутствует).
2. Запустить корутину при необходимости появления/исчезновения.
3. Интерполяция альфы каждый кадр приближает значение к целевому.
4. Переключать `interactable` и `blocksRaycasts` только когда элемент видим.

## Пример

```csharp
using System.Collections;
using UnityEngine;

public class UIFader : MonoBehaviour
{
    [SerializeField] private CanvasGroup canvasGroup;
    [SerializeField] private float fadeDuration = 0.4f;

    private Coroutine currentFade;

    private void Awake()
    {
        if (!canvasGroup)
        {
            canvasGroup = GetComponent<CanvasGroup>();
        }

        if (!canvasGroup)
        {
            canvasGroup = gameObject.AddComponent<CanvasGroup>();
        }
    }

    public void FadeIn()
    {
        StartFade(1f);
    }

    public void FadeOut()
    {
        StartFade(0f);
    }

    private void StartFade(float targetAlpha)
    {
        if (currentFade != null)
        {
            StopCoroutine(currentFade);
        }

        currentFade = StartCoroutine(FadeRoutine(targetAlpha));
    }

    private IEnumerator FadeRoutine(float targetAlpha)
    {
        float startAlpha = canvasGroup.alpha;
        float time = 0f;

        bool show = targetAlpha > 0.99f;
        canvasGroup.interactable = show;
        canvasGroup.blocksRaycasts = show;

        while (time < fadeDuration)
        {
            time += Time.unscaledDeltaTime;
            float t = Mathf.Clamp01(time / fadeDuration);
            canvasGroup.alpha = Mathf.Lerp(startAlpha, targetAlpha, t);
            yield return null;
        }

        canvasGroup.alpha = targetAlpha;
        canvasGroup.interactable = targetAlpha > 0.99f;
        canvasGroup.blocksRaycasts = targetAlpha > 0.99f;
        currentFade = null;
    }
}
```

### Примечания

- Замените `Time.unscaledDeltaTime` на `Time.deltaTime`, если хотите, чтобы затухание подчинялось изменениям масштаба времени.
- Для мгновенного переключения (например, начального состояния) устанавливайте `canvasGroup.alpha` напрямую перед запуском затухания.
- Интегрируйте с UI-событиями: вызывайте `FadeIn`, когда игрок входит в триггер, и `FadeOut` после таймаута или выхода.
- Если не хотите использовать корутины, управляйте затуханием в `Update`, интерполируя к целевой альфе с собственным таймером.
