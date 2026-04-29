# Unity Шпаргалка

Исчерпывающее руководство по паттернам и практикам разработки в Unity.

## Содержание

### Основы

- [MonoBehaviour](basics/monobehaviour.md)
  - [Обзор жизненного цикла](basics/monobehaviour.md#Обзор-жизненного-цикла)
  - [Корутины](basics/monobehaviour.md#Корутины)
  - [Управление порядком выполнения](basics/monobehaviour.md#Управление-порядком-выполнения)
  - [Лучшие практики](basics/monobehaviour.md#Лучшие-практики)
  - [Частые ошибки](basics/monobehaviour.md#Частые-ошибки)
- [Transform](basics/transform.md)
  - [Позиция](basics/transform.md#Позиция)
  - [Вращение](basics/transform.md#Вращение)
  - [Масштаб](basics/transform.md#Масштаб)
  - [Иерархии и Родительство](basics/transform.md#Иерархии-и-Родительство)
  - [Преобразования Пространства](basics/transform.md#Преобразования-Пространства)
  - [Рекомендации](basics/transform.md#Рекомендации)
- [Vector3](basics/vector3.md)
  - [Создание векторов](basics/vector3.md#Создание-векторов)
  - [Базовые математические операции](basics/vector3.md#Базовые-математические-операции)
  - [Скалярное произведение](basics/vector3.md#Скалярное-произведение-выравнивание-и-углы)
  - [Векторное произведение](basics/vector3.md#Векторное-произведение-построение-базиса-поверхности)
  - [Длина и нормализация](basics/vector3.md#Длина-и-нормализация)
  - [Вспомогательные методы для движения и интерполяции](basics/vector3.md#Вспомогательные-методы-для-движения-и-интерполяции)
  - [Проекция, отражение и работа в плоскости](basics/vector3.md#Проекция-отражение-и-работа-в-плоскости)
- [Quaternion](basics/quaternion.md)
  - [Когда использовать кватернионы](basics/quaternion.md#Когда-использовать-кватернионы)
  - [Создание вращений](basics/quaternion.md#Создание-вращений)
  - [Составление и применение вращений](basics/quaternion.md#Составление-и-применение-вращений)
  - [Интерполяция и ограничение](basics/quaternion.md#Интерполяция-и-ограничение)
  - [Анализ и преобразование](basics/quaternion.md#Анализ-и-преобразование)
  - [Рекомендации и отладка](basics/quaternion.md#Рекомендации-и-отладка)
- [Углы Эйлера](basics/euler-angles.md)
  - [Где Углы Эйлера Незаменимы](basics/euler-angles.md#Где-Углы-Эйлера-Незаменимы)
  - [Чтение и Задание Значений Эйлера](basics/euler-angles.md#Чтение-и-Задание-Значений-Эйлера)
  - [Создание Кватернионов из Данных Эйлера](basics/euler-angles.md#Создание-Кватернионов-из-Данных-Эйлера)
  - [Как Избежать Распространённых Ошибок](basics/euler-angles.md#Как-Избежать-Распространённых-Ошибок)
  - [Отладка и Утилиты](basics/euler-angles.md#Отладка-и-Утилиты)

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
  - [Добавление Rigidbody](physics/rigidbody.md#Добавление-Rigidbody)
  - [Основные свойства](physics/rigidbody.md#Основные-свойства)
  - [Перемещение Rigidbody](physics/rigidbody.md#Перемещение-Rigidbody)
  - [Режимы симуляции](physics/rigidbody.md#Режимы-симуляции)
  - [Сон и активация](physics/rigidbody.md#Сон-и-активация)
  - [Настройка столкновений](physics/rigidbody.md#Настройка-столкновений)
  - [Лучшие практики](physics/rigidbody.md#Лучшие-практики)
- [Коллизии и триггеры](physics/collisions.md)
  - [Коллизия vs Триггер: Когда что использовать](physics/collisions.md#Коллизия-vs-Триггер-Когда-что-использовать)
  - [3D События Коллизии](physics/collisions.md#3D-События-Коллизии)
  - [3D События Триггера](physics/collisions.md#3D-События-Триггера)
  - [2D События Коллизии](physics/collisions.md#2D-События-Коллизии)
  - [Общие паттерны](physics/collisions.md#Общие-паттерны)
  - [Распространённые ошибки](physics/collisions.md#Распространённые-ошибки)
- [Raycast](physics/raycast.md)
  - [Базовый Пример](physics/raycast.md#Базовый-Пример)
  - [Работа с Масками Слоёв](physics/raycast.md#Работа-с-Масками-Слоёв)
  - [Чтение Данных RaycastHit](physics/raycast.md#Чтение-Данных-RaycastHit)
  - [Варианты и Перегрузки](physics/raycast.md#Варианты-и-Перегрузки)
  - [Эквивалент для 2D Физики](physics/raycast.md#Эквивалент-для-2D-Физики)
  - [Лучшие Практики](physics/raycast.md#Лучшие-Практики)
- [Игнорирование коллизий](physics/ignore-collision.md)
  - [Physics.IgnoreCollision](physics/ignore-collision.md#PhysicsIgnoreCollision)
  - [Physics.IgnoreLayerCollision](physics/ignore-collision.md#PhysicsIgnoreLayerCollision)
  - [Эквиваленты для 2D](physics/ignore-collision.md#Эквиваленты-для-2D)

### Ввод

- [Клавиатура](input/keyboard.md)
- [Мышь](input/mouse.md)
- [Тач](input/touch.md)
- [Новая система ввода](input/new-input-system.md)
  - [Устаревший и новый менеджер ввода](input/new-input-system.md#Устаревший-и-новый-менеджер-ввода)
  - [Установка и настройка](input/new-input-system.md#Установка-и-настройка)
  - [Прямой доступ к устройствам](input/new-input-system.md#Прямой-доступ-к-устройствам)
  - [Действия ввода (Input Actions)](input/new-input-system.md#Действия-ввода-Input-Actions)
  - [Использование Input Actions в коде](input/new-input-system.md#Использование-Input-Actions-в-коде)
  - [Коллбэки против опроса](input/new-input-system.md#Коллбэки-против-опроса)
  - [Практические примеры](input/new-input-system.md#Практические-примеры)
  - [Переназначение клавиш во время выполнения](input/new-input-system.md#Переназначение-клавиш-во-время-выполнения)
  - [Лучшие практики](input/new-input-system.md#Лучшие-практики)
  - [Краткий справочник](input/new-input-system.md#Краткий-справочник)

### Интерфейс

- [Кнопка](ui/button.md)
- [Ползунок](ui/slider.md)

### Аудио

- [Базовое воспроизведение аудио](audio/basic-audio-play.md)
  - [Воспроизведение](audio/basic-audio-play.md#Воспроизведение)
  - [Пауза и остановка](audio/basic-audio-play.md#Пауза-и-остановка)
  - [Управление громкостью](audio/basic-audio-play.md#Управление-громкостью)

### Скриптинг

- [Корутины](scripting/coroutines.md)
  - [Базовый пример корутины](scripting/coroutines.md#Базовый-пример-корутины)
  - [Использование корутин для повторяющихся действий](scripting/coroutines.md#Использование-корутин-для-повторяющихся-действий)
  - [Ожидание условия](scripting/coroutines.md#Ожидание-условия)
  - [Использование корутин с событиями Unity](scripting/coroutines.md#Использование-корутин-с-событиями-Unity)
  - [Остановка корутин](scripting/coroutines.md#Остановка-корутин)
- [Async/Await](scripting/asyncawait.md)
  - [Базовая структура](scripting/asyncawait.md#Базовая-структура)
  - [Загрузка ресурсов](scripting/asyncawait.md#Загрузка-ресурсов)
  - [Веб-запросы](scripting/asyncawait.md#Веб-запросы)
  - [Загрузка сцен](scripting/asyncawait.md#Загрузка-сцен)
  - [Параллельные операции](scripting/asyncawait.md#Параллельные-операции)
  - [Обработка таймаута](scripting/asyncawait.md#Обработка-таймаута)
  - [Лучшие практики](scripting/asyncawait.md#Лучшие-практики)
- [Системы событий](scripting/event-systems.md)
  - [UnityEvents](scripting/event-systems.md#UnityEvents)
  - [События и делегаты C#](scripting/event-systems.md#События-и-делегаты-C)
- [Скриптовые объекты](scripting/scriptable-objects.md)
- [Пользовательские скрипты редактора](scripting/custom-editor-scripts.md)

### Паттерны проектирования

- [Одиночка (Singleton)](design-patterns/singleton.md)
- [Фабричный метод (Factory Pattern)](design-patterns/factory-pattern.md)
- [Наблюдатель (Observer Pattern)](design-patterns/observer-pattern.md)
- [Команда (Command Pattern)](design-patterns/command-pattern.md)
- [Состояние (State Pattern)](design-patterns/state-pattern.md)
  - [Базовый пример](design-patterns/state-pattern.md#Базовый-пример)
  - [Подробный пример — система обучения (онбординга) в игре](design-patterns/state-pattern.md#Подробный-пример-система-обучения-онбординга-в-игре)
- [Стратегия (Strategy Pattern)](design-patterns/strategy-pattern.md)
  - [Базовый пример](design-patterns/strategy-pattern.md#Базовый-пример)
  - [Подробный пример — Боевая система](design-patterns/strategy-pattern.md#Подробный-пример-Боевая-система)
- [Пул объектов (Object Pooling Pattern)](design-patterns/object-pooling-pattern.md)
- [Цепочка обязанностей (Chain of Responsibility Pattern)](design-patterns/chain-of-responsibility-pattern.md)
  - [Базовый пример](design-patterns/chain-of-responsibility-pattern.md#Базовый-пример)
  - [Подробный пример — система обработки ввода](design-patterns/chain-of-responsibility-pattern.md#Подробный-пример-система-обработки-ввода)

### Горячие клавиши

- [Редактирование в окне сцены](shortcuts/scene-view-editing.md)
- [Навигация в окне сцены](shortcuts/scene-view-navigation.md)
- [Управление иерархией](shortcuts/hierarchy-management.md)
- [Разметка](shortcuts/layout.md)

### Практические примеры

- [Проверка, стоит ли объект на земле](practical-use-cases/check-if-object-is-on-the-ground.md)
  - [Поведение](practical-use-cases/check-if-object-is-on-the-ground.md#Поведение)
  - [Пример](practical-use-cases/check-if-object-is-on-the-ground.md#Пример)
- [Получение трансформа кости тела](practical-use-cases/get-the-transform-of-a-body-bone.md)
  - [Поведение](practical-use-cases/get-the-transform-of-a-body-bone.md#Поведение)
  - [Пример](practical-use-cases/get-the-transform-of-a-body-bone.md#Пример)
- [Поворот объекта лицом к камере](practical-use-cases/make-object-look-at-the-camera.md)
  - [Поведение](practical-use-cases/make-object-look-at-the-camera.md#Поведение)
  - [Пример](practical-use-cases/make-object-look-at-the-camera.md#Пример)
- [Следование и облёт камеры](practical-use-cases/camera-follow-orbit.md)
  - [Поведение](practical-use-cases/camera-follow-orbit.md#Поведение)
  - [Пример](practical-use-cases/camera-follow-orbit.md#Пример)
- [Затухание UI-элемента](practical-use-cases/fade-ui-element.md)
  - [Поведение](practical-use-cases/fade-ui-element.md#Поведение)
  - [Пример](practical-use-cases/fade-ui-element.md#Пример)
- [Загрузка следующей сцены](practical-use-cases/load-next-scene.md)
  - [Поведение](practical-use-cases/load-next-scene.md#Поведение)
  - [Пример](practical-use-cases/load-next-scene.md#Пример)

## Избранные практические примеры

- [`Проверка, стоит ли объект на земле`](practical-use-cases/check-if-object-is-on-the-ground.md) – Проверка приземления через raycast с маской слоёв.
- [`Следование и облёт камеры`](practical-use-cases/camera-follow-orbit.md) – Плавное следование камеры с поддержкой орбитального вращения.
- [`Затухание UI-элемента`](practical-use-cases/fade-ui-element.md) – Плавное изменение прозрачности `CanvasGroup` через корутину.
- [`Поворот объекта лицом к камере`](practical-use-cases/make-object-look-at-the-camera.md) – Поведение Billboard с опциональной блокировкой осей.
- [`Загрузка следующей сцены`](practical-use-cases/load-next-scene.md) – Безопасная загрузка следующей сцены с проверкой индекса.
- [`Получение трансформа кости тела`](practical-use-cases/get-the-transform-of-a-body-bone.md) – Прикрепление объектов к костям гуманоидного персонажа.
