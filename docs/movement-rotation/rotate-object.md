# Unity — Поворот объекта: руководство по методам вращения

#### Transform.rotation
```csharp
// Quaternion хранит вращение Transform в мировом пространстве.
// Кватернионы основаны на комплексных числах и не страдают от проблемы gimbal lock.
// Unity использует кватернионы внутренне для представления всех вращений.

transform.rotation = new Quaternion(rotx, roty, rotz, rotw);
```

#### Transform.eulerAngles
```csharp
// Transform.eulerAngles представляет вращение в мировом пространстве.
// Важно понимать, что хотя вы задаёте значения вращения по X, Y и Z,
// они не хранятся напрямую. Вместо этого X, Y и Z конвертируются во внутренний формат Quaternion.

transform.eulerAngles = Vector3(rotx, roty, rotz);
```

#### Transform.Rotate()
```csharp
// Применяет вращение вокруг всех указанных осей.
public void Rotate(Vector3 eulers, Space relativeTo = Space.Self);
public void Rotate(float xAngle, float yAngle, float zAngle, Space relativeTo = Space.Self);

transform.Rotate(rotx, roty, rotz);
```

#### Transform.RotateAround()
```csharp
// Вращает transform вокруг оси, проходящей через точку в мировых координатах, на заданный угол в градусах.
public void RotateAround(Vector3 point, Vector3 axis, float angle);

// Вращаем объект вокруг цели со скоростью 20 градусов в секунду.
Transform target;
transform.RotateAround(target.position, Vector3.up, 20 * Time.deltaTime);
```

#### Transform.LookAt()
```csharp
// Направляет положительную сторону 'Z' (вперёд) объекта на позицию в мировом пространстве.
public void LookAt(Transform target);
public void LookAt(Transform target, Vector3 worldUp = Vector3.up);

// Поворачиваем вектор forward объекта так, чтобы он смотрел на целевой Transform.
Transform target;
transform.LookAt(target);

// То же, что выше, но передача Vector3.left в worldUp наклоняет объект на бок.
transform.LookAt(target, Vector3.left);
```

#### Quaternion.LookRotation()
```csharp
// Создаёт вращение с заданными направлениями вперёд и вверх.
public static Quaternion LookRotation(Vector3 forward, Vector3 upwards = Vector3.up);

// Следующий код поворачивает объект в сторону целевого объекта.
Vector3 direction = target.position - transform.position;
Quaternion rotation = Quaternion.LookRotation(direction);
transform.rotation = rotation;
```

#### Quaternion.FromToRotation()
```csharp
// Создаёт вращение (Quaternion), которое поворачивает из fromDirection в toDirection.
public static Quaternion FromToRotation(Vector3 fromDirection, Vector3 toDirection);

// Устанавливает вращение так, чтобы ось Y transform совпадала с осью Z.
transform.rotation = Quaternion.FromToRotation(Vector3.up, transform.forward);
```

#### Quaternion.ToAngleAxis()
```csharp
// Преобразует вращение в представление угол-ось (углы в градусах).
// Иными словами, извлекает угол и ось, которые представляет данный кватернион.
public void ToAngleAxis(out float angle, out Vector3 axis);

// Извлекаем представление угол-ось из вращения transform
float angle = 0.0f;
Vector3 axis = Vector3.zero;
transform.rotation.ToAngleAxis(out angle, out axis);
```

