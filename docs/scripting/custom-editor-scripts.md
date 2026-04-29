# Unity Custom Editor Scripts - Расширения инспектора

```csharp
// Скрипты Custom Editor позволяют создавать пользовательские инспекторы и окна в редакторе Unity.
using UnityEditor;
using UnityEngine;

[CustomEditor(typeof(MyComponent))]
public class MyComponentEditor : Editor {
    public override void OnInspectorGUI() {
        DrawDefaultInspector();

        MyComponent myComponent = (MyComponent)target;
        if (GUILayout.Button("Do Something")) {
            myComponent.DoSomething();
        }
    }
}
```

