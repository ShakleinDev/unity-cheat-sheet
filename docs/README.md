# Unity Шпаргалка

Исчерпывающее руководство по паттернам и практикам разработки в Unity.

## Содержание

### Основы

- [MonoBehaviour](basics/monobehaviour.md)
  - [Обзор жизненного цикла](basics/monobehaviour.md#обзор-жизненного-цикла)
  - [Корутины](basics/monobehaviour.md#корутины)
  - [Управление порядком выполнения](basics/monobehaviour.md#управление-порядком-выполнения)
  - [Лучшие практики](basics/monobehaviour.md#лучшие-практики)
  - [Частые ошибки](basics/monobehaviour.md#частые-ошибки)
- [Transform](basics/transform.md)
  - [Позиция](basics/transform.md#позиция)
  - [Вращение](basics/transform.md#вращение)
  - [Масштаб](basics/transform.md#масштаб)
  - [Иерархии и Родительство](basics/transform.md#иерархии-и-родительство)
  - [Преобразования Пространства](basics/transform.md#преобразования-пространства)
  - [Рекомендации](basics/transform.md#рекомендации)
- [Vector3](basics/vector3.md)
  - [Создание векторов](basics/vector3.md#создание-векторов)
  - [Базовые математические операции](basics/vector3.md#базовые-математические-операции)
  - [Скалярное произведение](basics/vector3.md#скалярное-произведение-выравнивание-и-углы)
  - [Векторное произведение](basics/vector3.md#векторное-произведение-построение-базиса-поверхности)
  - [Длина и нормализация](basics/vector3.md#длина-и-нормализация)
  - [Вспомогательные методы для движения и интерполяции](basics/vector3.md#вспомогательные-методы-для-движения-и-интерполяции)
  - [Проекция, отражение и работа в плоскости](basics/vector3.md#проекция-отражение-и-работа-в-плоскости)
- [Quaternion](basics/quaternion.md)
  - [Когда использовать кватернионы](basics/quaternion.md#когда-использовать-кватернионы)
  - [Создание вращений](basics/quaternion.md#создание-вращений)
  - [Составление и применение вращений](basics/quaternion.md#составление-и-применение-вращений)
  - [Интерполяция и ограничение](basics/quaternion.md#интерполяция-и-ограничение)
  - [Анализ и преобразование](basics/quaternion.md#анализ-и-преобразование)
  - [Рекомендации и отладка](basics/quaternion.md#рекомендации-и-отладка)
- [Углы Эйлера](basics/euler-angles.md)
  - [Где Углы Эйлера Незаменимы](basics/euler-angles.md#где-углы-эйлера-незаменимы)
  - [Чтение и Задание Значений Эйлера](basics/euler-angles.md#чтение-и-задание-значений-эйлера)
  - [Создание Кватернионов из Данных Эйлера](basics/euler-angles.md#создание-кватернионов-из-данных-эйлера)
  - [Как Избежать Распространённых Ошибок](basics/euler-angles.md#как-избежать-распространённых-ошибок)
  - [Отладка и Утилиты](basics/euler-angles.md#отладка-и-утилиты)

### Движение и вращение

- [Перемещение объекта](movement-rotation/move-object.md)
  - [Transform.Translate()](movement-rotation/move-object.md#transformtranslate)
  - [Vector3.MoveTowards()](movement-rotation/move-object.md#vector3movetowards)
  - [Vector3.Lerp()](movement-rotation/move-object.md#vector3lerp)
  - [Vector3.SmoothDamp()](movement-rotation/move-object.md#vector3smoothdamp)
- [Вращение объекта](movement-rotation/rotate-object.md)
  - [Transform.rotation](movement-rotation/rotate-object.md#transformrotation)
  - [Transform.eulerAngles](movement-rotation/rotate-object.md#transformeulerangles)
  - [Transform.Rotate()](movement-rotation/rotate-object.md#transformrotate)
  - [Transform.RotateAround()](movement-rotation/rotate-object.md#transformrotatearound)
  - [Transform.LookAt()](movement-rotation/rotate-object.md#transformlookat)
  - [Quaternion.LookRotation()](movement-rotation/rotate-object.md#quaternionlookrotation)
  - [Quaternion.FromToRotation()](movement-rotation/rotate-object.md#quaternionfromtorotation)
  - [Quaternion.ToAngleAxis()](movement-rotation/rotate-object.md#quaterniontoangleaxis)

### Физика

- [Rigidbody](physics/rigidbody.md)
  - [Добавление Rigidbody](physics/rigidbody.md#добавление-rigidbody)
  - [Основные свойства](physics/rigidbody.md#основные-свойства)
  - [Перемещение Rigidbody](physics/rigidbody.md#перемещение-rigidbody)
  - [Режимы симуляции](physics/rigidbody.md#режимы-симуляции)
  - [Сон и активация](physics/rigidbody.md#сон-и-активация)
  - [Настройка столкновений](physics/rigidbody.md#настройка-столкновений)
  - [Лучшие практики](physics/rigidbody.md#лучшие-практики)
- [Коллизии и триггеры](physics/collisions.md)
  - [Коллизия vs Триггер: Когда что использовать](physics/collisions.md#коллизия-vs-триггер-когда-что-использовать)
  - [3D События Коллизии](physics/collisions.md#3d-события-коллизии)
  - [3D События Триггера](physics/collisions.md#3d-события-триггера)
  - [2D События Коллизии](physics/collisions.md#2d-события-коллизии)
  - [Общие паттерны](physics/collisions.md#общие-паттерны)
  - [Распространённые ошибки](physics/collisions.md#распространённые-ошибки)
- [Raycast](physics/raycast.md)
  - [Базовый Пример](physics/raycast.md#базовый-пример)
  - [Работа с Масками Слоёв](physics/raycast.md#работа-с-масками-слоёв)
  - [Чтение Данных RaycastHit](physics/raycast.md#чтение-данных-raycasthit)
  - [Варианты и Перегрузки](physics/raycast.md#варианты-и-перегрузки)
  - [Эквивалент для 2D Физики](physics/raycast.md#эквивалент-для-2d-физики)
  - [Лучшие Практики](physics/raycast.md#лучшие-практики)
- [Игнорирование коллизий](physics/ignore-collision.md)
  - [Physics.IgnoreCollision](physics/ignore-collision.md#physicsignorecollision)
  - [Physics.IgnoreLayerCollision](physics/ignore-collision.md#physicsignorelayercollision)
  - [Эквиваленты для 2D](physics/ignore-collision.md#эквиваленты-для-2d)

### Ввод

- [Клавиатура](input/keyboard.md)
- [Мышь](input/mouse.md)
- [Тач](input/touch.md)
- [Новая система ввода](input/new-input-system.md)
  - [Устаревший и новый менеджер ввода](input/new-input-system.md#устаревший-и-новый-менеджер-ввода)
  - [Установка и настройка](input/new-input-system.md#установка-и-настройка)
  - [Прямой доступ к устройствам](input/new-input-system.md#прямой-доступ-к-устройствам)
  - [Действия ввода (Input Actions)](input/new-input-system.md#действия-ввода-input-actions)
  - [Использование Input Actions в коде](input/new-input-system.md#использование-input-actions-в-коде)
  - [Коллбэки против опроса](input/new-input-system.md#коллбэки-против-опроса)
  - [Практические примеры](input/new-input-system.md#практические-примеры)
  - [Переназначение клавиш во время выполнения](input/new-input-system.md#переназначение-клавиш-во-время-выполнения)
  - [Лучшие практики](input/new-input-system.md#лучшие-практики)
  - [Краткий справочник](input/new-input-system.md#краткий-справочник)

### Интерфейс

- [Кнопка](ui/button.md)
- [Ползунок](ui/slider.md)

### Аудио

- [Базовое воспроизведение аудио](audio/basic-audio-play.md)
  - [Воспроизведение](audio/basic-audio-play.md#воспроизведение)
  - [Пауза и остановка](audio/basic-audio-play.md#пауза-и-остановка)
  - [Управление громкостью](audio/basic-audio-play.md#управление-громкостью)

### Скриптинг

- [Корутины](scripting/coroutines.md)
  - [Базовый пример корутины](scripting/coroutines.md#базовый-пример-корутины)
  - [Использование корутин для повторяющихся действий](scripting/coroutines.md#использование-корутин-для-повторяющихся-действий)
  - [Ожидание условия](scripting/coroutines.md#ожидание-условия)
  - [Использование корутин с событиями Unity](scripting/coroutines.md#использование-корутин-с-событиями-unity)
  - [Остановка корутин](scripting/coroutines.md#остановка-корутин)
- [Async/Await](scripting/asyncawait.md)
  - [Базовая структура](scripting/asyncawait.md#базовая-структура)
  - [Загрузка ресурсов](scripting/asyncawait.md#загрузка-ресурсов)
  - [Веб-запросы](scripting/asyncawait.md#веб-запросы)
  - [Загрузка сцен](scripting/asyncawait.md#загрузка-сцен)
  - [Параллельные операции](scripting/asyncawait.md#параллельные-операции)
  - [Обработка таймаута](scripting/asyncawait.md#обработка-таймаута)
  - [Лучшие практики](scripting/asyncawait.md#лучшие-практики)
- [Системы событий](scripting/event-systems.md)
  - [UnityEvents](scripting/event-systems.md#unityevents)
  - [События и делегаты C#](scripting/event-systems.md#события-и-делегаты-c)
- [Скриптовые объекты](scripting/scriptable-objects.md)
- [Пользовательские скрипты редактора](scripting/custom-editor-scripts.md)

### Паттерны проектирования

- [Одиночка (Singleton)](design-patterns/singleton.md)
- [Фабричный метод (Factory Pattern)](design-patterns/factory-pattern.md)
- [Наблюдатель (Observer Pattern)](design-patterns/observer-pattern.md)
- [Команда (Command Pattern)](design-patterns/command-pattern.md)
- [Состояние (State Pattern)](design-patterns/state-pattern.md)
  - [Базовый пример](design-patterns/state-pattern.md#базовый-пример)
  - [Подробный пример — система обучения (онбординга) в игре](design-patterns/state-pattern.md#подробный-пример--система-обучения-онбординга-в-игре)
- [Стратегия (Strategy Pattern)](design-patterns/strategy-pattern.md)
  - [Базовый пример](design-patterns/strategy-pattern.md#базовый-пример)
  - [Подробный пример — Боевая система](design-patterns/strategy-pattern.md#подробный-пример--боевая-система)
- [Пул объектов (Object Pooling Pattern)](design-patterns/object-pooling-pattern.md)
- [Цепочка обязанностей (Chain of Responsibility Pattern)](design-patterns/chain-of-responsibility-pattern.md)
  - [Базовый пример](design-patterns/chain-of-responsibility-pattern.md#базовый-пример)
  - [Подробный пример — система обработки ввода](design-patterns/chain-of-responsibility-pattern.md#подробный-пример--система-обработки-ввода)

### Горячие клавиши

- [Редактирование в окне сцены](shortcuts/scene-view-editing.md)
- [Навигация в окне сцены](shortcuts/scene-view-navigation.md)
- [Управление иерархией](shortcuts/hierarchy-management.md)
- [Разметка](shortcuts/layout.md)

### Практические примеры

- [Проверка, стоит ли объект на земле](practical-use-cases/check-if-object-is-on-the-ground.md)
  - [Поведение](practical-use-cases/check-if-object-is-on-the-ground.md#поведение)
  - [Пример](practical-use-cases/check-if-object-is-on-the-ground.md#пример)
- [Получение трансформа кости тела](practical-use-cases/get-the-transform-of-a-body-bone.md)
  - [Поведение](practical-use-cases/get-the-transform-of-a-body-bone.md#поведение)
  - [Пример](practical-use-cases/get-the-transform-of-a-body-bone.md#пример)
- [Поворот объекта лицом к камере](practical-use-cases/make-object-look-at-the-camera.md)
  - [Поведение](practical-use-cases/make-object-look-at-the-camera.md#поведение)
  - [Пример](practical-use-cases/make-object-look-at-the-camera.md#пример)
- [Следование и облёт камеры](practical-use-cases/camera-follow-orbit.md)
  - [Поведение](practical-use-cases/camera-follow-orbit.md#поведение)
  - [Пример](practical-use-cases/camera-follow-orbit.md#пример)
- [Затухание UI-элемента](practical-use-cases/fade-ui-element.md)
  - [Поведение](practical-use-cases/fade-ui-element.md#поведение)
  - [Пример](practical-use-cases/fade-ui-element.md#пример)
- [Загрузка следующей сцены](practical-use-cases/load-next-scene.md)
  - [Поведение](practical-use-cases/load-next-scene.md#поведение)
  - [Пример](practical-use-cases/load-next-scene.md#пример)

## Избранные практические примеры

- [`Проверка, стоит ли объект на земле`](practical-use-cases/check-if-object-is-on-the-ground.md) – Проверка приземления через raycast с маской слоёв.
- [`Следование и облёт камеры`](practical-use-cases/camera-follow-orbit.md) – Плавное следование камеры с поддержкой орбитального вращения.
- [`Затухание UI-элемента`](practical-use-cases/fade-ui-element.md) – Плавное изменение прозрачности `CanvasGroup` через корутину.
- [`Поворот объекта лицом к камере`](practical-use-cases/make-object-look-at-the-camera.md) – Поведение Billboard с опциональной блокировкой осей.
- [`Загрузка следующей сцены`](practical-use-cases/load-next-scene.md) – Безопасная загрузка следующей сцены с проверкой индекса.
- [`Получение трансформа кости тела`](practical-use-cases/get-the-transform-of-a-body-bone.md) – Прикрепление объектов к костям гуманоидного персонажа.
