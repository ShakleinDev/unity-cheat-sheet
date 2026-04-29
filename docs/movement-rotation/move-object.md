# Unity — Перемещение объекта: методы Transform и Physics

#### Transform.Translate()
```csharp
// Перемещает transform в заданном направлении на указанное расстояние.
public void Translate(Vector3 translation);
public void Translate(Vector3 translation, Space relativeTo = Space.Self);

transform.Translate(Vector3.right * movementSpeed);
```

#### Vector3.MoveTowards()
```csharp
// Вычисляет позицию между точками current и target,
// перемещаясь не дальше, чем указано в maxDistanceDelta.
public static Vector3 MoveTowards(Vector3 current, Vector3 target, float maxDistanceDelta);

Vector3 targetPosition;
transform.position = Vector3.MoveTowards(transform.position, targetPosition, Time.deltaTime);
```

#### Vector3.Lerp()
```csharp
// Линейная интерполяция между двумя точками. Даёт плавный переход.
public static Vector3 Lerp(Vector3 startValue, Vector3 endValue, float interpolationRatio);

Vector3 targetPosition;
float t = 0;
t += Time.deltaTime * speed;
transform.position = Vector3.Lerp(transform.position, targetPosition, t);
```

#### Vector3.SmoothDamp()
```csharp
// Плавно изменяет вектор в сторону целевого значения со временем.
// Вектор сглаживается функцией, похожей на пружинный демпфер, и никогда не превысит цель.
// Чаще всего используется для плавного следования камеры за объектом.
public static Vector3 SmoothDamp(Vector3 current, Vector3 target, ref Vector3 currentVelocity, float smoothTime, float maxSpeed = Mathf.Infinity, float deltaTime = Time.deltaTime);

float smoothTime = 1f;
Vector3 velocity;
Vector3 targetPosition = target.TransformPoint(new Vector3(0, 5, -10));
// Плавно перемещаем камеру к целевой позиции
transform.position = Vector3.SmoothDamp(transform.position, targetPosition, ref velocity, smoothTime);
```

