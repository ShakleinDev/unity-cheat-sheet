# Unity Transform — Позиция, Вращение и Масштаб

Каждый GameObject в Unity имеет компонент `Transform`. Он хранит позицию, вращение и масштаб объекта, а также определяет иерархии родитель-потомок. Уверенное владение математикой трансформаций необходимо для размещения объектов, перемещения персонажей и выравнивания UI.

## Позиция

- `transform.position` — координаты в мировом пространстве.
- `transform.localPosition` — позиция относительно родительского трансформа.
- `transform.Translate(...)` — перемещает трансформ на вектор, опционально в локальном или мировом пространстве.

```csharp
// Переместить на 2 единицы вперёд в локальном пространстве объекта.
transform.Translate(Vector3.forward * 2f);

// Позиция: Vector3, представляющий координаты в мировом пространстве (x, y, z).
transform.position = new Vector3(0f, 2f, 0f);

// Позиция относительно родительского трансформа.
transform.localPosition = Vector3.zero;

// Перевод из локальных координат в мировые с помощью TransformPoint.
Vector3 worldSlot = transform.TransformPoint(new Vector3(0f, 1f, 0f));
```

Используйте `TransformPoint` / `InverseTransformPoint` при работе со смещениями относительно родителей или сокетов (например, крепления оружия).

## Вращение

Unity предоставляет вращения как в виде углов Эйлера (в градусах), так и кватернионов. Предпочитайте кватернионы при интерполяции или совмещении вращений во избежание шарнирного замка (gimbal lock).

- `transform.rotation` — кватернион в мировом пространстве.
- `transform.localRotation` — вращение относительно родителя.
- `transform.eulerAngles` — быстрый доступ к мировым углам Эйлера; при изменении пересчитывает кватернион.
- `transform.LookAt(target)` — удобный способ направить вектор forward к заданной точке.

```csharp
// Повернуть на 90 градусов вокруг оси Y в мировом пространстве.
transform.rotation = Quaternion.Euler(0f, 90f, 0f);

// Плавно повернуть в сторону цели.
Quaternion targetRotation = Quaternion.LookRotation(target.position - transform.position);
transform.rotation = Quaternion.Slerp(transform.rotation, targetRotation, Time.deltaTime * turnSpeed);

// Вращать на 45 градусов в секунду вокруг локальной оси X.
transform.Rotate(Vector3.right * 45f * Time.deltaTime, Space.Self);
```

## Масштаб

- `transform.localScale` — масштаб относительно родителя; по умолчанию `(1,1,1)`.
- Избегайте отрицательного или нулевого масштаба на игровых объектах: это может перевернуть нормали и нарушить физику.
- При анимации масштаба или использовании дочерних иерархий рассмотрите нормализацию масштаба родителя до `(1,1,1)` и применение изменений на дочерних объектах.

```csharp
// Увеличить размер вдвое равномерно.
transform.localScale = Vector3.one * 2f;

// Пульсация масштаба со временем.
float pulse = 1f + Mathf.Sin(Time.time) * 0.2f;
transform.localScale = new Vector3(pulse, pulse, pulse);
```

## Иерархии и Родительство

- `transform.SetParent(newParent, worldPositionStays)` — смена родителя с опциональным сохранением мирового трансформа.
- Доступ к дочерним объектам через `transform.GetChild(index)` или перебором `foreach (Transform child in transform)`.
- Будьте осторожны при масштабировании родителей: дочерние трансформы наследуют масштаб и вращение.

```csharp
// Прикрепить подбираемый объект к руке, сохраняя текущую мировую позицию.
pickupTransform.SetParent(handTransform, worldPositionStays: true);

// Отсоединить от родителя и сбросить локальный трансформ.
transform.SetParent(null);
transform.localPosition = Vector3.zero;
transform.localRotation = Quaternion.identity;
transform.localScale = Vector3.one;
```

## Преобразования Пространства

- `TransformDirection` — преобразует локальный вектор направления в мировое пространство без изменения его длины.
- `InverseTransformDirection` — мировое → локальное направление.
- `TransformVector` / `InverseTransformVector` — аналогично, но с учётом масштаба.

```csharp
// Выпустить луч прямо вперёд в мировом пространстве, используя локальный forward.
Vector3 worldForward = transform.TransformDirection(Vector3.forward);
Physics.Raycast(transform.position, worldForward, out var hit, 10f);

// Выровнять UI-элемент по направлению камеры, спроецированному на горизонтальную плоскость.
Vector3 cameraForward = cameraTransform.forward;
Vector3 planarForward = Vector3.ProjectOnPlane(cameraForward, Vector3.up).normalized;
transform.rotation = Quaternion.LookRotation(planarForward);
```

## Просмотр Трансформов в Редакторе

- Переключайте **локальный/глобальный гизмо** (горячая клавиша `X` в новых версиях Unity или иконка на панели инструментов) для перемещения в нужной системе координат.
- Используйте переключатель **Pivot/Center** для настройки точки опоры гизмо при выборе нескольких объектов.
- Для точности вводите числовые значения непосредственно в инспекторе. Сброс до значений по умолчанию — через иконку шестерёнки > `Reset`.

## Рекомендации

- Сохраняйте масштаб корневого GameObject равным `(1,1,1)`, применяя визуальное масштабирование к дочерним мешам.
- Избегайте прямого присваивания `transform.parent`; предпочитайте `SetParent` с `worldPositionStays` для контроля над изменением трансформов.
- Кэшировать `transform` в горячих циклах? Не нужно — Unity уже кэширует его внутри, однако сохраняйте ссылки на другие компоненты (`transform.parent`, поиск дочерних объектов), если они используются часто.
- При управлении объектами через физику изменяйте `Rigidbody` с помощью `MovePosition` / `MoveRotation`, а не трансформ напрямую.

Уверенное владение трансформами открывает возможности для точного позиционирования, плавного вращения и чистых иерархий во всём проекте. Сочетайте эти инструменты с математическими вспомогательными классами (`Vector3`, `Quaternion`) для построения точной игровой механики.
